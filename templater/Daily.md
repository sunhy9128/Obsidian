<%*
let today = tp.date.now("YYYY-MM-DD")
let tag_name = await tp.system.prompt("Tag")
let subject_name = await tp.system.prompt("Subject")
let inputDate = await tp.system.prompt("输入示例："+today,today)
titleName = window.moment(inputDate, "YYYY-MM-DD", true).format("YYYY-MM-DD")+"-"+subject_name

let createTime = tp.file.creation_date()
let modificationDate = tp.file.last_modified_date("dddd Do MMMM YYYY HH:mm:ss")
-%>

---
create time : <% createTime %>
modification date: <% modificationDate %>

---
## <% subject_name %>  |  <% tp.user.weather() %>
#<% tag_name %>

> <% tp.web.daily_quote() %>


<%*
await app.vault.createFolder("/DAILY/"+today)
await tp.file.move("/DAILY/"+ today + "/" + titleName)
tp.file.cursor()
-%>