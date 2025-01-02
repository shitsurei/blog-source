
---
title: 排行榜
date: 2018-10-18 17:41:21
type: "top"
comments: false
---


<div id="top"></div>
<script src="//cdn1.lncld.net/static/js/3.0.4/av-min.js"></script>
<script>AV.initialize("cNz5k6f57dqTeXsddEX0mI0b-gzGzoHsz", "cc2GBT1kcfpFrQn5CSeB2Hfj");</script>
<script type="text/javascript">
  var time=0
  var title=""
  var url=""
  var query = new AV.Query('Counter');
  query.notEqualTo('id',0);
  query.descending('time');
  query.limit(100);
  query.find().then(function (todo) {
   for (var i=0;i<100;i++){
   var result=todo[i].attributes;
   time=result.time;
   title=result.title;
   url=result.url;
   var content="<a href='"+"https://shitsurei.online"+url+"'>"+title+"</a>"+"<br />"+"<font color='#555'>"+"阅读次数："+time+"</font>"+"<br /><br />";
      //var content="<p>"+"<font color='#1C1C1C'>"+"【文章热度:"+time+"℃】"+"</font>"+"<a href='"+"https://shitsurei.online"+url+"'>"+title+"</a>"+"</p>";
      document.getElementById("top").innerHTML+=content
    }
  }, function (error) {
  console.log("error");
  });
</script>

<style>.post-description { display: none; }</style>
