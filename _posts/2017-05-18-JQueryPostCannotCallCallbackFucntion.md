---
layout: post
title: JQuery post或ajax方法发送请求成功后不能成功回调的解决
categories: [Q&A, JQuery]
description: JQuery post或ajax方法发送请求成功后不能成功回调的解决
keywords: JQuery,Ajax,Post
---

# JQuery post或ajax方法发送请求成功后不能成功回调的解决

使用JQuery访问服务器数据时，请求可以成功返回，返回的内容也没有问题，但是死活不能调用回调函数
服务器接收请求并生成json方法如下：
```Java
@RequestMapping("/incSharedDocDownloadCountAndReturnTheNewValue.do")
public void download(HttpServletRequest request, HttpServletResponse response)
{
  // path是指欲下载的文件的路径。
  String idStr = request.getParameter("docId");

  int count = 0;
  String status = "";
  String message = "";

  SharedDocument sDoc = null;
  try
  {
    int docId = Integer.parseInt(idStr);
    sDoc = sharedDocumentService.findSharedDocumentByiId(docId);

    if (null == sDoc)
    {
      status = "fail";
    } else
    {

      Integer currentCountObject = sDoc.getDownload_count();
      if (null != currentCountObject)
      {
        count = currentCountObject;
      }

      count++;
      // 设置新值
      sDoc.setDownload_count(count);
      // 更新sDoc
      sharedDocumentService.editSharedDocument(sDoc, " 1=1", 1, 10);
      status = "success";
    }

  } catch (Exception e)
  {
    status = "fail";
    // TODO: handle exception
  }

  String resultJson = "{\"count\":" + count + ", \"status\":'" + status + "', \"message\":'" + message + "'}";
  response.setCharacterEncoding("UTF-8");
  response.setContentType("application/json; charset=utf-8");
  PrintWriter out = null;
  try
  {
    out = response.getWriter();
    out.write(resultJson);
  } catch (IOException e)
  {
    e.printStackTrace();
  } finally
  {
    if (out != null)
    {
      out.close();
    }
  }
}
```

前端Ajax代码
```JavaScript
function downloadSharedFile(filePath, fileName, sharedDocId)
{
  //Ajax 增长下载次数并获取最新的数据库文档下载次数
  /***
  $.ajax({
      type: 'POST',
      url: 'incSharedDocDownloadCountAndReturnTheNewValue.do',
      data: {docId:sharedDocId},
      success: function(data){
      debugger;
      alert(data);
      },
      dataType:'json'
    });
  **/

  $.post("incSharedDocDownloadCountAndReturnTheNewValue.do", {docId:sharedDocId},
  function(data)
  {
    eval("var result=" + data);
    //alert("Count: " + result.count + ", Status: " + result.status);
    $("#downloadCountSpan" + sharedDocId).text(result.count);
    var targetUrl = "downloadFileByPath.do?filepath=" + filePath + "&fileName=" + fileName;
    window.open(targetUrl);
  }
  );
}
```
CSDN有人说返回的json所有的key都应该用双引号引起来, 然并卵,  
不知道到底哪里的问题， 只能使用下面的权宜之计来解析返回的数据了：

1. 将post的dataType设置为"text";
2. 通过eval重新解析json内容
```JavaScript
$.post("incSharedDocDownloadCountAndReturnTheNewValue.do", {docId:sharedDocId},
function(data)
{
  eval("var result=" + data);
  //alert("Count: " + result.count + ", Status: " + result.status);
  $("#downloadCountSpan" + sharedDocId).text(result.count);
  var targetUrl = "downloadFileByPath.do?filepath=" + filePath + "&fileName=" + fileName;
  window.open(targetUrl);
}, "text"
);
```
问题勉强解决, 有空再分析具体原因吧
