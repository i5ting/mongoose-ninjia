# mongoose-ninjia



不存在

    SendDeliveryHistory.find({
      date: current_date, 
      status: 0,
      delivery_number : {
        $exists: false 
      }
    }).
    
存在，但size == 0的
    
    SendDeliveryHistory.find({
      date: current_date, 
      status: 0,
      delivery_number : {
        $not: {$size: 0}
      }
    }).