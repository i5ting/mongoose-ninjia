# mongoose-ninjia


## mongoexport转义

注意，如果-q后面的条件是用“”双引号括起来的话$符需要做一下转义\$

mongoexport -d xbm-wechat-api -c senddeliveryhistories -o senddeliveryhistories.json --jsonArray --query "{date:{\$in:['20150825','20150826']}}"


##

mongodb自带的 new date了，他的时间是utc的时间，和咱们中国时区少8个小时

### 不存在

    SendDeliveryHistory.find({
      date: current_date, 
      status: 0,
      delivery_number : {
        $exists: false 
      }
    }).
    
### 存在，但size == 0的
    
    SendDeliveryHistory.find({
      date: current_date, 
      status: 0,
      delivery_number : {
        $not: {$size: 0}
      }
    }).
    
### and 和 or

    db.senddeliveryhistories.find({
        "$and": [
            {
                created_at:{
                    "$gt": ISODate("2015-08-19T12:30:18.595Z")
                }
            }
            ,
            {
                created_at:{
                    "$lt": ISODate("2015-08-20T12:30:18.595Z")
                }
            }
        ]
    })
    
    
    SendDeliveryHistory.find({
      status: 0,
      delivery_number : {
        $exists: false 
      }
    }).and([{ created_at: {
      "$gt": new Date(2015,07,19,22)
    } }, { created_at: {
       "$lt": new Date(2015,07,20,13,55)
    } }]).then(function(SendDeliveryHistoryArr) {
        var callback_data = [];
        console.log(SendDeliveryHistoryArr)
    });
    
说明

- new Date(2015,07,20,13,55)
  - 2015年
  - 07实际是8月
  - 20日
  - 20点
  - 13分
  - 55秒
  
## eq


MongoDB没有"$eq"操作符,但是只有一个元素的"$in"的操作效果是一样的


## 数组查询


数据如下

    /* 0 */
    {
        "_id" : ObjectId("55d83d7af492ea270c7812dc"),
        "status" : 0,
        "items" : [ 
            {
                "product" : ObjectId("55c9d91cb372a223713be929"),
                "name" : "眼贴",
                "count" : "3"
            }
        ],
        "type" : 0,
        "__v" : 0
    }

做法

    db.senddeliveryhistories.find({
        "items.0.name":{$in: ["眼贴"]}
    })

## 不等于

      remark: {
        "$ne": "转货"
      } 
      
## between and

可以翻译成lt and gt


db.getCollection('orders').find({"$and":[
      {status2:1},
      {status:'0'}
 ]}).count()
 
## mongoose用$push的时候，都会创建自动_id，能否不创建
   
var LogSchema = new Schema({
	contents:   	[{
		            	string:        { type: String },
		            	levle:           { type: Number },
	                	}],
});

Log.findByIdAndUpdate( id , { $push : { string : ‘123’ , level : 1 } } ,  { upsert : true } , callback );

执行后，数据库内容：

{ “id” : ObjectId("…"),“contents” : [ { “level” : 1, “content” : “123”, “_id” : ObjectId(“568137b4873c0d3b1ead3d1d”) }]  }

“_id” : ObjectId(“568137b4873c0d3b1ead3d1d”) ，我不想要，能否去掉？

在schema设定的时候直接_id:false
 
 https://cnodejs.org/topic/568139e1b9de25e81e01c1b5#568152ffb9de25e81e01c1bd