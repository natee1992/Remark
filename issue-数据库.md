### mongodb集群分片后count()计数错误

> 描述
  在mongodb集群分片后，执行db.collection.find().count()命令，返回的条数和数据库实际存储数量对应错误
  此现象在mongodb官方文档中有记录（https://docs.mongodb.com/manual/reference/method/db.collection.count/）
  
> 原因
  1. 操作的是分片的集合（前提）；
  2. shard分片正在做块迁移，导致有重复数据出现
  3. 存在孤立文档（因为不正常关机、块迁移失败等原因导致）
  
> 解决方案
  1. 使用aggregate聚合后使用{$group:{'_id': '','count':{'$sum': 1}}}
  2. 使用db.collection.validate({full:true})进行全量验证扫描 . db.runCommand({validate:"collection",full:true})
