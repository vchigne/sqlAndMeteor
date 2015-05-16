# sqlAndMeteor
On the diverse ways to use SQL on Meteor - As 16-05-2015

If you are like me, you love Meteor but hate Mongo.
In Meteor's Trello Roadmap (https://trello.com/b/hjBDflxp/meteor-roadmap), the most voted feature is SQL Support, either PostgreSQL or MySQL.

Since there is no date for that in Meteor, here I summarize the partial solutions I have found.

<h2>1.- Use SQL only for  client-side querys. </h2>

  Let's face it, Mongo sucks on common data operations, so having the ability to use SQL to query data (with JOINS, GRUP BY and so on) would relief a lot of pain.
  There are packages which let you use SQL in the client, at least for querys:
  The simplest one is a old (2010) utility, SqlLike (http://www.thomasfrank.se/sqlike.html). 
  The new player in town is alaSQL, which is actively developed by @agershun (https://github.com/agershun/alasql).
  The SqlLike advantage is that it only has 10k. AlaSQL, is a lot more powerful, of course, but for using SQL to replace  mongo sintax in unions and aggregations, SqlLike is OK. 
  
  You can install alaSQL with  
  
      meteor add agershun:alasql
  
  With both of them you can do something like this in your helper:
    
    productsSold:function(){
      var customerSalesHistory=salesHistory.find({cutomerId:Session.get('currentCustomer')}).fetch();
      var items=products.find().fetch();
      return alasql("select item.name, sales.ordered as sumaVentas from ? sales, ? items
          where items.Id=sales.itemId",[customerSalesHistory,items]);
    }
Update:  alaSQL has just added support for Meteor Mongo Collections:

AlaSQL and Meteor Mongo collections

Now you can use Meteor collections as agruments. To do it simply store alasql.min.js to the client/lib directory and then apply SQL to Meteor Collections:

    Template.body.helpers({
       tasks: function () {
         return alasql('SELECT * FROM ?',[Tasks]);
       }
    });
Or you can use with find() options with special METEOR() from-function:

    return alasql('SELECT * FROM ?',[Tasks]);
    return alasql('SELECT * FROM METEOR(?)',[Tasks]);
    return alasql('SELECT * FROM METEOR(?,?)',[Tasks,{text:"Hello world!"}]);
    return alasql('SELECT * FROM METEOR(?,{text:"Hello world!"})',[Tasks]);

Strongly reccomended.

<h2>2.- Experiment with direct SQL support.</h2>
Some packages try to replace Mongo (and minimongo) with MySql or PostgreSQL. 
@numtel's MySql package is  Meteor-MySql https://github.com/numtel/meteor-mysql, and PostgreSQL is Meteor-pg (https://github.com/numtel/meteor-pg). Both are good attempts to solve the problem, but have some issues yet and are somehow cumbersome to adapt. 

A team from Hack Reactor has formed Meteor Stream, and its first product is a PostgreSql integration with Meteor, meteor-postgres (https://github.com/meteor-stream/meteor-postgres).  It looks very good and uses alaSql on the client to replace minimongo.

Both approaches are good, but they have some problems:
- They broke deployment to meteor. 
- They are very, very young and not near production ready AFAIK
- They still require tweaks to the usual pub-sub sintax we are used to, which could raise compatibility issues with other meteor packages.

<h2>3.- Still use Mongo, but as simple repository for your MySql database.</h2>

This option maintains all Meteor's characteristics and uses Mongo as a temporal repository for your MySql or PostgreSql databases. 

A  brilliant attempt to that is mysql-shadow by @perak (https://github.com/perak/mysql-shadow). It does what it says, keeps Mongo synchronized both ways with MySql and let's you work your data in MySql.

The bad news is that the developer will not continue maintaining it, but what is done is enough to work with simple scenarios where you don't have complex triggers that update other tables or stuff like that.

For a full featured synchronization you can use SymmetricsDS (http://www.symmetricds.org), a very well tested database replicator. This involves setting up a new java server, of course, but is by far the best way to be sure that you will be able to convert your Mongo database in a simple repository of your real MySql, PostgreSQL, SQL Server , Informix database. I have to check it myself yet.

For now MySQL Shadow seems like a good enough solution.

One advantage of this approach is that you can still use all standard Meteor features, packages, meteor deployment and so on. You don´t have to do anything but set up the synch mechanism, and you are not breaking anything.

Also, if someday the Meteor team uses some of the dollars raised in SQL integration, your app is more likely to work as is.

Please add resources or strategies you may know.


