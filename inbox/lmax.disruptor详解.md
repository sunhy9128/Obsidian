## **Disruptor术语说明**
- Ring Buffer
如其名，环形的缓冲区。曾经 RingBuffer 是 Disruptor 中的最主要的对象，但从3.0版本开始，其职责被简化为仅仅负责对通过 Disruptor 进行交换的数据（事件）进行存储和更新。在一些更高级的应用场景中，Ring Buffer 可以由用户的自定义实现来完全替代。
- Sequence Disruptor
通过顺序递增的序号来编号管理通过其进行交换的数据（事件），对数据(事件)的处理过程总是沿着序号逐个递增处理。一个 Sequence 用于跟踪标识某个特定的事件处理者( RingBuffer/Consumer )的处理进度。虽然一个 AtomicLong 也可以用于标识进度，但定义 Sequence 来负责该问题还有另一个目的，那就是防止不同的 Sequence 之间的CPU缓存伪共享(Flase Sharing)问题。
（注：这是 Disruptor 实现高性能的关键点之一，网上关于伪共享问题的介绍已经汗牛充栋，在此不再赘述）。
- Sequencer
Sequencer 是 Disruptor 的真正核心。此接口有两个实现类 SingleProducerSequencer、MultiProducerSequencer ，它们定义在生产者和消费者之间快速、正确地传递数据的并发算法。
- Sequence Barrier
用于保持对RingBuffer的 main published Sequence 和Consumer依赖的其它Consumer的 Sequence 的引用。 Sequence Barrier 还定义了决定 Consumer 是否还有可处理的事件的逻辑。
- Wait Strategy
定义 Consumer 如何进行等待下一个事件的策略。 （注：Disruptor 定义了多种不同的策略，针对不同的场景，提供了不一样的性能表现）
- Event
在 Disruptor 的语义中，生产者和消费者之间进行交换的数据被称为事件(Event)。它不是一个被 Disruptor 定义的特定类型，而是由 Disruptor 的使用者定义并指定。
- EventProcessor
EventProcessor 持有特定消费者(Consumer)的 Sequence，并提供用于调用事件处理实现的事件循环(Event Loop)。
- EventHandler
Disruptor 定义的事件处理接口，由用户实现，用于处理事件，是 Consumer 的真正实现。
- Producer
即生产者，只是泛指调用 Disruptor 发布事件的用户代码，Disruptor 没有定义特定接口或类型。

![[Pasted image 20220221142957.png]]

### Demo
```java
@Data

public class FaceSearchEvent {

	private FaceSearchMessage result;

	@Data

	@AllArgsConstructor

	@NoArgsConstructor

	public static class FaceSearchMessage {

		private byte[] imageContent;

		private String deviceSerialNo;

		private JSONObject comment;
	}

}

public class FaceSearchEventFactory implements EventFactory<FaceSearchEvent> {

	@Override

	public FaceSearchEvent newInstance() {

		return new FaceSearchEvent();
		
	}
}


@Component

public class FaceSearchHandler implements EventHandler<FaceSearchEvent> {

	private static final Logger LOGGER = LoggerFactory.getLogger(FaceSearchHandler.class);

	@Override

	public void onEvent(FaceSearchEvent event, long sequence, boolean endOfBatch) throws Exception {

		FaceSearchEvent.FaceSearchMessage message = event.getResult();
		// TODO
	}
}


@Component

public class FaceSearchDisruptorQueue {

	private static final Logger LOGGER = LoggerFactory.getLogger(FaceSearchDisruptorQueue.class);

	private static RingBuffer<FaceSearchEvent> ringBuffer;
  

	@Autowired

	FaceSearchDisruptorQueue(FaceSearchHandler eventHandler) {

		int bufferSize = 32 * 1024 * 32;

		ThreadFactory executor = Executors.defaultThreadFactory();

		Disruptor<FaceSearchEvent> disruptor = new Disruptor<>(new FaceSearchEventFactory(), bufferSize, executor,

		ProducerType.MULTI, new BlockingWaitStrategy());

		disruptor.handleEventsWith(eventHandler);

		ringBuffer = disruptor.getRingBuffer();

		disruptor.setDefaultExceptionHandler(new IgnoreExceptionHandler());

		disruptor.start();

	}

	public static void publishEvent(FaceSearchEvent.FaceSearchMessage log) {

		long sequence = ringBuffer.next();

		try {

			FaceSearchEvent event = ringBuffer.get(sequence);

			event.setResult(log);

		} finally {

			ringBuffer.publish(sequence);
		}

	}

}
```

### RingBuffer
![[Pasted image 20220221144930.png]]

Disruptor使用一个Ring Buffer存放生产者的“产品”，环形缓冲区实际上还是一段连续内存，之所以称作环形是因为它对数据存放位置的处理，生产者和消费者各有一个指针（数组下标），消费者的指针指向下一个要读取的Slot，生产者指针指向下一个要放入的Slot，消费或生产后，各自的指针值p = (p +1) % n，n是缓冲区长度，这样指针在缓冲区上反复游走，故可以将缓冲区看成环状。（如图）（Ring Buffer并非Disruptor原创，Linux内核中就有环形缓冲区的实现）使用Ring Buffer时：
1. 当生产者和消费者都只有一个时，由于两个线程分别操作不同的指针，所以不需要锁。
2. 当有多个消费者时，（按Disruptor的设计）每个消费者各自控制自己的指针，依次读取每个Slot（也就是每个消费者都会读取到所有的产品），这时只需要保证生产者指针不会超过最慢的消费者（超过最后一个消费者“一圈”）即可，也不需要锁。
3. 当有多个生产者时，多个线程共用一个写指针，此处需要考虑多线程问题，例如两个生产者线程同时写数据，当前写指针=0，运行后其中一个线程应获得缓冲区0号Slot，另一个应该获得1号，写指针=2。对于这种情况，Disruptor使用CAS来保证多线程安全。


**CAS(Compare and Swap/Set)**
CAS是现在CPU普遍支持的一种指令（例如cmpxchg系类指令），CAS操作包含3个操作数：CAS(A,B,C)，其功能是：取地址A的值与B比较，如果相同，则将C赋值到地址A。CAS特点是它是由硬件实现的极轻量级指令，同时CPU也保证此操作的原子性。在考虑线程间同步问题时，可以使用Unsafe类的boolean compareAndSwapInt(java.lang.Object arg0, long arg1, int arg2, int arg3);系列方法，对于一个int变量（例如，Ring Buffer的写指针），使用CAS可以避免多线程访问带来的混乱，当compareAndSwap方法true时表明CAS操作成功赋值，返回false则表明地址A处的值并不等于B，此时重新试一遍即可。