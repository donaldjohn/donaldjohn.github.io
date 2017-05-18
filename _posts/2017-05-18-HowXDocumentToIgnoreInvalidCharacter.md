---
layout: post
title: CSharp解析格式不良好XML全攻略
categories: [C#, XML]
description: C#, XDocument, XML
keywords: \\0xu0001, C#, XDocument, 无效字符
---

* TOC
{:toc}
# C# 解析格式不良好XML全攻略


## C#中XDocument解析XML提示无效字符的解决办法
用XDocument解析XML时个别文件报字符无效错误。
### 忽略无效字符
有种思路是替换无效的字符。

最简单的方法当然是忽略无效字符，具体操作如下
```CSharp
XDocument xDocument = null;
XmlReaderSettings xmlReaderSettings = new XmlReaderSettings { CheckCharacters = false };
using (XmlReader xmlReader = XmlReader.Create(filename, xmlReaderSettings))
{
    xmlReader.MoveToContent();
    xDocument = XDocument.Load(xmlReader);
}
```
### 替换低位字符
对于低位字符的错误只能通过字符替换的方式实现了:
```CSharp
MemoryStream memStream = new MemoryStream();
entry.WriteTo(memStream);
StreamReader reader = new StreamReader(memStream);
if (memStream.Position > 0) { memStream.Position = 0; }
var xmlDocContent = reader.ReadToEnd();
var regx = new Regex(@"[\x00-\x08]|[\x0B-\x0C]|[\x0E-\x1F]");
xmlDocContent = regx.Replace(xmlDocContent, " ");
MemoryStream newMemStream = new MemoryStream(ASCIIEncoding.UTF8.GetBytes(xmlDocContent));
if (newMemStream.Position > 0)
{
    newMemStream.Position = 0;
}
XmlReaderSettings xmlReaderSettings = new XmlReaderSettings { CheckCharacters = false, DtdProcessing = DtdProcessing.Ignore };
XmlReader xmlReader = XmlReader.Create(newMemStream, xmlReaderSettings);
xmlReader.MoveToContent();

XDocument doc = null;
try
{
    doc = XDocument.Load(xmlReader);
}
catch (Exception ex)
{
    var message = $"加载XML文件发生错误{filePath}\\{entry.Key}";
    throw new Exception(message + ex.Message, ex.InnerException);
}
```
上面的代码同时忽略掉了无效的DTD

## 使用NSoup工具
JSoup是Java里优秀的用来解析XML的工具，C#里有对应的实现NSoup.
如果要解析的XML格式实在是太混乱, 还是使用NSoup解析吧，对格式不良好甚至无效的XML都有很好的解析能力, 支持CSS选择器语法：
### 声明NSoup对象
```CSharp
MemoryStream memStream = new MemoryStream();
entry.WriteTo(memStream);
StreamReader reader = new StreamReader(memStream);
if (memStream.Position > 0) { memStream.Position = 0; }
var xmlDocContent = reader.ReadToEnd();
var docSoup = NSoup.NSoupClient.Parse(xmlDocContent);
entityObject.STA_PUB_COUNTRY = MiscUtil.getEleHtmlWithAncestorInfoAndTargetInfoByNSoup(docSoup, "business:PublicationReference", "dataFormat", "standard", "base:WIPOST3Code");
```
### NSoup工具方法参考
解析工具方法如下:
```CSharp
public static string getEleHtmlWithAncestorInfoAndTargetInfoByNSoup(NSoup.Nodes.Document doc, string ancestorTabName, string ancestorAttrName, string ancestorAttrValue, string targetTagName, string targetAttrName = "", string targetAttrValue = "", bool getHtml = false)
        {
            string targetValue = "";

            var ancestor = (from ele in doc.GetElementsByTag(ancestorTabName)
                            where
                                string.IsNullOrEmpty(ancestorAttrName) ||
                                (!string.IsNullOrEmpty(ancestorAttrName) &&
                                ancestorAttrValue.Equals(ele.Attr(ancestorAttrName)))
                            select ele
                            ).FirstOrDefault();

            if (null != ancestor)
            {
                var target = (from ele in ancestor.GetElementsByTag(targetTagName)
                              where
                              string.IsNullOrEmpty(targetAttrName) ||
                              (!string.IsNullOrEmpty(targetAttrName) &&
                               targetAttrValue.Equals(ele.Attr(targetAttrName)))
                              select ele
                               ).FirstOrDefault();
                if (null != target)
                {
                    if (getHtml)
                    {
                        targetValue = target.Html();
                    }
                    else
                    {
                        targetValue = target.Text();
                    }
                }
            }
            return targetValue;
        }

        public static string getEleHtmlOnlyWithTargetInfoByNSoup(NSoup.Nodes.Document doc, string targetTagName, string targetAttrName = "", string targetAttrValue = "", bool getHtml = false)
        {
            string targetValue = "";

            var target = (from ele in doc.GetElementsByTag(targetTagName)
                          where
                          string.IsNullOrEmpty(targetAttrName) ||
                          (!string.IsNullOrEmpty(targetAttrName) &&
                           targetAttrValue.Equals(ele.Attr(targetAttrName)))
                          select ele
                           ).FirstOrDefault();

            if (null != target)
            {
                if (getHtml)
                {
                    targetValue = target.Html();
                }
                else
                {
                    targetValue = target.Text();
                }
            }
            return targetValue;
        }

        public static string getEleAttrOnlyWithTargetInfoByNSoup(NSoup.Nodes.Document doc, string targetTagName, string targetAttrName)
        {
            string targetAttrValue = "";
            var target = (from ele in doc.GetElementsByTag(targetTagName)
                          select ele
                           ).FirstOrDefault();

            if (null != target)
            {
                targetAttrValue = target.Attr(targetAttrName);
            }
            return targetAttrValue;
        }

        public static string getSingleEleHtmlOrAttrWithCssSelectorByNSoup(NSoup.Nodes.Document doc, string selector, string targetAttrName = "", bool getHtml = false)
        {
            string targetAttrValue = "";
            var target = (from ele in doc.Select(selector)
                          select ele
                           ).FirstOrDefault();

            if (null != target)
            {
                if (string.IsNullOrEmpty(targetAttrName))
                {
                    if (getHtml)
                    {
                        targetAttrValue = target.Html();
                    }
                    else
                    {
                        targetAttrValue = target.Text();
                    }
                }
                else
                {
                    targetAttrValue = target.Attr(targetAttrName);
                }
            }
            return targetAttrValue;
        }

        public static string getMultiEleHtmlOrAttrWithCssSelectorByNSoupSepratedByDoubleColon(NSoup.Nodes.Document doc, string selector, string targetAttrName = "", bool getHtml = false)
        {
            string result = "";

            var targetEnum = from ele in doc.Select(selector)
                             select ele;

            if (targetEnum.Count() > 0)
            {
                if (string.IsNullOrEmpty(targetAttrName))
                {
                    if (getHtml)
                    {
                        result = string.Join(";;", from ele in targetEnum
                                                   where !string.IsNullOrEmpty(ele.Html())
                                                   select ele.Html());
                    }
                    else
                    {
                        result = string.Join(";;", from ele in targetEnum
                                                   where !string.IsNullOrEmpty(ele.Html())
                                                   select ele.Text());
                    }
                }
                else
                {
                    result = string.Join(";;", from ele in targetEnum
                                               where !string.IsNullOrEmpty(ele.Attr(targetAttrName))
                                               select ele.Attr(targetAttrName));
                }
            }

            return result;
        }

        public static string getEleAttrWithAncestorInfoAndTargetInfoByNSoup(NSoup.Nodes.Document doc, string ancestorTabName, string ancestorAttrName, string ancestorAttrValue, string targetTagName, string targetAttrName)
        {
            string targetValue = "";

            var ancestor = (from ele in doc.GetElementsByTag(ancestorTabName)
                            where
                                string.IsNullOrEmpty(ancestorAttrName) ||
                                (!string.IsNullOrEmpty(ancestorAttrName) &&
                                ancestorAttrValue.Equals(ele.Attr(ancestorAttrName)))
                            select ele
                            ).FirstOrDefault();

            if (null != ancestor)
            {
                var target = (from ele in ancestor.GetElementsByTag(targetTagName)
                              select ele
                               ).FirstOrDefault();

                if (null != target)
                {
                    targetValue = target.Attr(targetAttrName);
                }
            }
            return targetValue;
        }
```
