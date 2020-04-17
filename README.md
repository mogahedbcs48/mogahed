import 'package:flutter/material.dart';
import 'package:new_news/pages/newsList.dart';
import 'package:new_news/vewArticlesModels/newsArticlesListView.dart';
import 'package:provider/provider.dart';


void main() => runApp(App());


class App extends StatelessWidget {
@override
Widget build(BuildContext context){
  return MaterialApp(
title: 'new news',
// الداله بتاعه التغير دي بتشيل من home  وتديها للطفلchild
home: ChangeNotifierProvider(
    
 //  builder: (context), 
   create: (BuildContext context) =>NewsArticleListViewModel(),
child: NewsList(),
),
  );
}
}


////////////////////////////////////////
 

 class NewsArticle{
final String title;
final String descripion;

final String urlToImage;

final String url;
NewsArticle({this.title,this.descripion,this.urlToImage,this.url});
factory NewsArticle.fromJason(Map<String,dynamic>json){
return NewsArticle(
title: json['title'],
descripion: json['description'],
url: json['url'],
urlToImage: json['urlToImage']


);
}
 }
 
 
 /////////////////////////
 



import 'package:flutter/material.dart';
import 'package:new_news/vewArticlesModels/newsArticlesListView.dart';
import 'package:provider/provider.dart';
class NewsList extends StatefulWidget{
  @override
  _NewsListState  createState() =>_NewsListState();
}

class _NewsListState extends State<NewsList> {
  
  @override
  void initState() {
    super.initState();
    Provider.of<NewsArticleListViewModel>(context ,listen: false)
    .poplateTopHeadLine();
  }
 
  @override
  Widget build(BuildContext context) {
    final vs = Provider.of<NewsArticleListViewModel>(context);
    return Scaffold(
      appBar: AppBar(title: Text("New news"),),
      body: ListView.builder(

        itemCount: vs.articles.length,
        itemBuilder: (context,index){
         final short = vs.articles[index];
          return ListTile(
            leading: Container( 
              width: 100,
              height: 150,
              child: short.imgeUrl ==null ? Image.network('https://scontent.fruh2-1.fna.fbcdn.net/v/t1.0-9/69456892_2232151700227354_5860663762234638336_n.jpg?_nc_cat=110&_nc_sid=85a577&_nc_ohc=b_xy2HCBdqUAX90BfGc&_nc_ht=scontent.fruh2-1.fna&oh=d490395837b7f52b868a475dbf915cf1&oe=5EC01F37'):
              Image.network(short.imgeUrl)),
                title: Text(short.title),
          );
        },
      ),
    );
  }
}


////////////////////////////

// this is for all news head line

import 'package:flutter/widgets.dart';
import 'package:new_news/models/news_articles.dart';
import 'package:new_news/vewArticlesModels/vewArticlesModel.dart';
import 'package:new_news/webservicess/webservic.dart';

class NewsArticleListViewModel extends ChangeNotifier{

List<NewsArticleViewModel> articles =List<NewsArticleViewModel>();
void poplateTopHeadLine()async{
  
  List <NewsArticle> newsArticles =await WebServic().fetchTopHeadline();

this.articles =newsArticles.map((article)=>NewsArticleViewModel(article: article)).toList();
notifyListeners();
}


}

/////////////////////////////
 // get the network requst and give us json resolt and give it to the NewsArticals
import 'dart:convert';

import 'package:http/http.dart' as http;
import 'package:new_news/models/news_articles.dart';

 class WebServic{
   Future<List<NewsArticle>> fetchTopHeadline()async{
String url="https://newsapi.org/v2/top-headlines?country=us&apiKey=10404980a7104bf29ff4db9cfc7ca436";
final response =await http.get(url);
if(response.statusCode==200){
 final result = jsonDecode(response.body);
 //دا لانو جوا كائن جوا مصفوفه
 Iterable list =result["articles"];
 return list.map((articale)=> 
 NewsArticle.fromJason(articale)).toList();
}else{
  throw Exception("Faild to get top News");
}
   }
 }

