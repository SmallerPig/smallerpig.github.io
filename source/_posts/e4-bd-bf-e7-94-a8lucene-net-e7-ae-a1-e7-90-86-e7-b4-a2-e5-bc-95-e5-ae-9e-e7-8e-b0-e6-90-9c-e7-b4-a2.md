---
title: 使用Lucene.Net管理索引实现搜索
id: 404
categories:
  - ASP.NET
  - 'C#'
  - SQL
  - WEB开发
date: 2014-01-19 15:17:43
tags:
---

之前使用一直是没有问题的，只到今天发现删除的时候无法删除，增加的时候却一直在增加，导致搜索的时候可以搜出来很多相同的结果。

小猪决定趁今天这个机会好好的把这个问题给解决了。
``` c#
private void ProcessJobs(IndexWriter writer)
{
    while (jobs.Count != 0)
    {
        IndexJob job = jobs.Dequeue\(\);
        writer.DeleteDocuments(new Term("Id", job.Id.ToString\(\)));//先执行删除的操作
        //如果“添加文章”任务再添加，
        if (job.JobType == JobType.Add)
        {
            BLL.BooksManage bll = new BLL.BooksManage\(\);
            Entity.Books art = bll.GetById(job.Id);
            if (art == null)//有可能刚添加就被删除了
            {
                continue;
            }

            //string channel_id = art.channel_id.ToString\(\);
            string title = art.Title;
            DateTime time = art.CreateDate;
            string content = Utils.DropHTML(art.Brief.ToString\(\));
            string Addtime = art.CreateDate.ToString("yyyy-MM-dd");

            Document document = new Document\(\);
            //只有对需要全文检索的字段才ANALYZED
            document.Add(new Field("Id", job.Id.ToString\(\), Field.Store.YES, Field.Index.NOT_ANALYZED));
            document.Add(new Field("Title", title, Field.Store.YES, Field.Index.ANALYZED, Lucene.Net.Documents.Field.TermVector.WITH_POSITIONS_OFFSETS));
            document.Add(new Field("Tag", art.Tag, Field.Store.YES, Field.Index.ANALYZED, Lucene.Net.Documents.Field.TermVector.WITH_POSITIONS_OFFSETS));
            document.Add(new Field("PubTime", art.PubTime.ToString("yyyy-MM-dd"), Field.Store.YES, Field.Index.NOT_ANALYZED));
            document.Add(new Field("Cover", art.Cover, Field.Store.YES, Field.Index.NOT_ANALYZED));
            document.Add(new Field("Author", art.Author == null ? "" : art.Author, Field.Store.YES, Field.Index.ANALYZED, Lucene.Net.Documents.Field.TermVector.WITH_POSITIONS_OFFSETS));
            document.Add(new Field("Translator", art.Translator == null ? "" : art.Translator, Field.Store.YES, Field.Index.ANALYZED, Lucene.Net.Documents.Field.TermVector.WITH_POSITIONS_OFFSETS));
            document.Add(new Field("Publisher", art.Publisher == null ? "" : art.Publisher, Field.Store.YES, Field.Index.ANALYZED, Lucene.Net.Documents.Field.TermVector.WITH_POSITIONS_OFFSETS));
            document.Add(new Field("Language", art.Language, Field.Store.YES, Field.Index.NOT_ANALYZED));
            document.Add(new Field("Brief", Utils.DropHTML(art.Brief.ToString\(\)), Field.Store.YES, Field.Index.ANALYZED, Lucene.Net.Documents.Field.TermVector.WITH_POSITIONS_OFFSETS));
            document.Add(new Field("Icon", art.Icon, Field.Store.YES, Field.Index.ANALYZED));
            document.Add(new Field("Rate", art.Rate.ToString\(\), Field.Store.YES, Field.Index.ANALYZED));
            document.Add(new Field("Price", art.Price.ToString\(\), Field.Store.YES, Field.Index.ANALYZED));
            document.Add(new Field("Device", art.Device, Field.Store.YES, Field.Index.ANALYZED));
            document.Add(new Field("EngineVersion", art.EngineVersion.ToString\(\), Field.Store.YES, Field.Index.ANALYZED));
            document.Add(new Field("ContentType", art.ContentType, Field.Store.YES, Field.Index.ANALYZED));
            document.Add(new Field("Size", art.Size.ToString\(\), Field.Store.YES, Field.Index.ANALYZED));
            document.Add(new Field("Status", art.Status.ToString\(\), Field.Store.YES, Field.Index.ANALYZED));
            document.Add(new Field("Other2", art.Other2, Field.Store.YES, Field.Index.ANALYZED));
            writer.AddDocument(document);
            logger.Debug("索引" + job.Id + "完毕");
        }
        else
        { 

        }

    }
}
```

之前小猪还在想为什么这里没有处理删除的逻辑，仔细看了下发现只要增加了任务不管是删除还是添加都会先执行删除操作以防止索引结果的重复。问题就出现在这里，依照什么规则来删除呢？之前小猪直接用的别人的代码，今天才发现需要自己定义删除的规则：
```
writer.DeleteDocuments(new Term("number", job.Id.ToString\(\)))
```

另外就是要处理好啥时候删除，啥时候增加的逻辑。不然很容易出现各种问题，例如数据库中没有而索引里有的，或者数据库里有的但是不可用的但是搜索出来的等等等等~~