# [Comme Chez Soi](https://axeleroy.github.io/commechezsoi/)
_Une version web de [#UnToitPourCaramel](https://github.com/axeleroy/untoitpourcaramel) 
这是我的上一个作品[#UnToitPourCaramel](https://github.com/axeleroy/untoitpourcaramel) 的网页版
–mon précédent logiciel d'aggrégation d'annonces immobilières– utilisant Angular 5 et AWS Lambda._
我的上个汇总租卖房屋信息软件，用angular5和aws lambda来实现
## Contexte （背景故事）
En 2016 j'ai programmé en Python [#UnToitPourCaramel](https://github.com/axeleroy/untoitpourcaramel)
afin de m'aider dans ma recherche d'appartement et après avoir trouvé grâce à lui 
l'appartement de mes rêves en moins d'une semaine, plusieurs de mes amis furent intéressés pour l'utiliser.
在2016年，我用python开发了[#UnToitPourCaramel](https://github.com/axeleroy/untoitpourcaramel) ，其目的是为了帮助我搜索房源，最终使用它，让我在不到一周的时间里就找到了很好的房子入手。之后，我会把该工具介绍给我周边的人。

Cependant, l'installation et l'utilisation de celui-ci étaient peu triviales pour ceux qui
avaient peu d'expérience technique.
但是，上一版本的安装使用对于那些没有技术根底的人来说很难使用。

Fin 2017, j'ai donc entrepris de réécrire complétement cette application afin de la rendre accessible à tous
à partir d'un simple navigateur web.
2017年底，我从新写了这个项目，目的是所有人都可以轻松的在自己选的浏览器来使用这个搜房工具。
## Fonctionnement
Cette application est composée de deux composants :
这个应用是有两部分组成的：
* Une **web-app Angular** en charge de :
* 一个是web app angular
  * Gérer et stocker (dans un `LocalStorage`) les critères de recherches
  * 在浏览器里的localstorage里管理和储存，搜索条件
  * Appeler les Lambdas AWS de récupération des annonces
  * 调用aws lambdas，让它去做租售房屋信息
  * Stocker (dans une `IndexedDB`) et afficher les annonces
  * 储存并显示这些房屋信息。
* Plusieurs **Lambdas AWS** en charge de :
* 多个aws lambdas 负责：
  * Récupérer les critères qui leurs sont envoyés
  * 收取从web端发来的搜索条件
  * Effectuer les requêtes vers les APIs des différents sites d'annonce
  * 把搜索条件从新打包，发送请求到不同的网络平台的api上获取发回信息：法国最流行的几个网站有：leboncoin.fr, bienici.fr, pap, logg-immo.fr
  * Parser les réponses et les retourner dans des objets `Annonce` 
  * 把各个网站的回复进行解析，然后生成一则annonce的类。
  
## Déploiement （部署）
### AWS Lambda （亚马逊lambda免服务器部署）
1. Se déplacer dans `/awslambda`
1. 我们移步来到 `/awslambda`
2. Modifier le champ `ORIGIN` de `src/main/java/sh.leroy.awel.commechezsoi.awslambda\Constants.java`
afin qu'il corresponde à l'URL (protocole et port compris) sur laquelle la web-app sera disponible
2. 修改在`src/main/java/sh.leroy.awel.commechezsoi.awslambda\Constants.java`文件里的 `ORIGIN`字段，为了让origin和平台的url一致。
3. Compiler les lambdas 
3. 编译这些lambdasjava方法
   ```bash
   mvn package
   ```
3. Installer `serverless` 
3. 安装  `serverless` 
   ```bash
   npm install -g serverless
   ```
4. Déployer
4. 部署
   ```bash
   export AWS_ACCESS_KEY_ID=<your-AMI-key-here>
   export AWS_SECRET_ACCESS_KEY=<your-AMI-secret-key-here>
   serverless deploy --stage [dev|production] --region [aws-region]
   ```
   et noter l'URL à laquelle les lambdas sont déployées.

### Angular 
1. Se déplacer dans `/angular`
1. 我们移步来到  `/angular`
2. Modifier le champ `aws_lambda_endpoint` dans `src/environments/environments.ts` afin qu'il
corresponde au à l'URL notée plus haut (ex : `https://xxxxxxx.execute-api.eu-west-2.amazonaws.com/production/`)
2.  `src/environments/environments.ts`文件里修改`aws_lambda_endpoint`，以至于他和平台url是一致的
3. Installer `angular-cli`
3. 安装 `angular-cli`
   ```bash
   npm install -g @angular/cli
   ````
4. Construire l'application
4. 组建应用
   ```bash
   ng build --prod
   ```
5. Copier le contenu de `dist` sur votre serveur ou un repo [GitHub Pages](https://pages.github.com/).
4. 拷贝内容到服务器上或是写在github里
 
## To-Do List
* Support de Bien'ici, LogicImmo et PAP
* bienici，logicimmo和pap支持
* Critères avancés (type de logement, commodités, etc.)
* 附加条件（房屋类型， 坐标 等）
* Tri des annonces 
* 信息排序
  * Par site （按网站的首字母 排序）
  * Par ville （按城市）
  * Par prix （价格）
  * Par surface （面积）
  * etc.
* Interface à la Trello 
* Récupération des coordonnées GPS et affichage sur une carte

## Crédits
* [ng-bootstrap](https://github.com/ng-bootstrap/ng-bootstrap) : Boostrap pour Angular et quelques
goodies (typeahead + datepicker)
* [angular-persistence](https://github.com/darkarena1/angular-persistence) : service de stockage 
via `LocalStorage`
* [Dexie.js](https://github.com/dfahlander/Dexie.js/) : wrapper pour `IndexedDB`
* [Font Awesome](https://fontawesome.com/v4.7.0/)
* [Serverless](https://serverless.com/) : framework de déploiement et gestion des Lambdas AWS
