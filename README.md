# sqlAndMeteor
On the diverse ways to use SQL on Meteor - As 05-05-2015

If you are like me, you love Meteor but hate Mongo.
In Meteor's Trello Roadmap (https://trello.com/b/hjBDflxp/meteor-roadmap), the most voted feature is SQL Support, either PostgreSQL or MySQL.

Since there is no date for that in Meteor, here I summarize the partial solutions I have found.

1.- Use SQL only in clent side for querys. 
  Let's face it, Mongo sucks on common data operations, so having the ability to use SQL to query data (with JOINS, GRUP BY and so on) would relief a lot of pain.
  There are packages which let you use SQL in the client, at least for querys:
  The simplest one is a old (2010) utility, SqlLike (http://www.thomasfrank.se/sqlike.html). 
  The new player in town is alaSQL, which is actively developed by @agershun (https://github.com/agershun/alasql).
  The SqlLike advantage is that it only has 10k. AlaSQL, is a lot more powerful, of course, but for using SQL to replace  mongo sintax in unions and aggregations, SqlLike is OK. 
  
  With both of them you can do something like this in your helper:
    
    productsSold:function(){
      var customerSalesHistory=salesHistory.find({cutomerId:Session.get('currentCustomer')}).fetch();
      var items=products.find().fetch();
      return alasql("select item.name, sales.ordered as sumaVentas from ? sales, ? items
          where items.Id=sales.itemId",[customerSalesHistory,items]);
    }

2.- Experiment with direct SQL support.

Those packages try to replace Mongo (and minimongo) with MySql or PostgreSQL. 

@numtel's MySql package is  Meteor-MySql https://github.com/numtel/meteor-mysql, and PostgreSQL is Meteor-pg (https://github.com/numtel/meteor-pg). Both are good attemps to solve the problem, but thay have issues yet and are somehow cumbersome to adapt. 

A team from Hack Reactor has formed Meteor Stream, and its first product is a PostgreSql integration with Meteor, meteor-postgres (https://github.com/meteor-stream/meteor-postgres).  It looks very good and uses alaSql on the client to replace minimongo.

Both approaches are good, but they have some problems. First, they broke deployment to meteor. Second, they are very, very young and not near production ready AFAIK,and third 

