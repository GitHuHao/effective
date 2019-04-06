# effective
An effective way to play in python world.

## You can call like these :
注意：插入语句如果带主键，主键自增，会导出冲突问题）
### 1) CRUD 传参兼容 
    query 直接传单参
    status,result =my.fly('select * from car where name="A100"')
    print(status,result)
    
    query 直接传单参
    print(my.fly('select * from car where price>1900'))
    print(status,result)
    
    query 错误传参 兼容值 （顺便封装对象）
    status,result = my.fly('select name,price,id from car where name=%s',params="A100",pojo=Car)
    print(status,result)
    
    query 错误传参 兼容 dict（顺便封装对象）
    status,result = my.fly('select price,id,name from car where name=%(name)s',params={'name':'A100'},pojo=Car)
    print(status,result)
    
    query 错误传参 兼容 ('A100',) 和 ('A100') （顺便封装对象）
    status,result = my.fly('select price,id,name from car where name=%s',params=('A100',),pojo=Car)
    print(status,result)

### 2）BATCH EXECUTE
    # execute insert update select delete （顺便封装对象）
    print(my.fly('insert into car(price,name) values (%s,%s)',params=(2500L,'A100')))
    print(my.fly('update car set price=%s where name=%s and price=%s', params=(1000,'A100',2500L)))
    print(my.fly('select price,id,name from car where name=%(name)s', params={'name': 'A100'}, pojo=Car)[1])
    print(my.fly('delete from car where name=%s',params='A100'))
    print(my.fly('select price,id,name from car where name=%(name)s', params={'name': 'A100'}, pojo=Car))
    
    # batch tuple insert
    params = [('A%s' % i, i) for i in range(1,1000000)]
    status, count = my.fly(sql='insert into car(name,price) values (%s,%s)', params=params)
    print(status, count)
    
    # batch dict insert
    param_dict = [{'id': i, 'name': 'A%s' % i, 'price': i} for i in range(1, 10000)]
    status, count = my.fly(sql='insert into car(name,price) values (%(name)s,%(price)s)', params=param_dict)
    print(status, count)
    
    # batch instance insert
    instances = [Car(price=i,name='A%s' % i, id=i) for i in range(0, 100000)]
    rows = my.fly(sql='insert into car(name,price) values (%s,%s)', params=instances, fields=['name','price'])
    print(rows)
    
    # batch instance delete 兼容 fields 异常传参
    instances = [Car(price=i,name='A%s' % i, id=i) for i in range(0, 2000)]
    rows = my.fly(sql='delete from car where name =%s', params=instances, fields='name')
    print(rows)

    # batch instance upsert
    instances = [Car(price=i,name='A%s' % i, id=i) for i in range(0, 4000)]
    rows = my.fly(sql='INSERT INTO car(name,price) VALUES (%s,%s) ON DUPLICATE KEY UPDATE name=VALUES(name)',
                  params=instances, fields=['name', 'price'])
    print(rows)
    
### 3) DDL & Instance
    CREATE TABLE `car` (
      `id` int(3) NOT NULL AUTO_INCREMENT,
      `name` varchar(50) DEFAULT NULL,
      `price` bigint(5) DEFAULT NULL,
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=2000 DEFAULT CHARSET=utf8;

    class Car:
        def __init__(self,price,id,name):
            self.price = price
            self.id = id
            self.name = name
            
        def __str__(self):
            return 'Car: id=%s, price=%s, name=%s'%(self.id,self.price,self.name)
    
        def __repr__(self):
            return 'Car: id=%s, price=%s, name=%s'%(self.id,self.price,self.name)
### End
