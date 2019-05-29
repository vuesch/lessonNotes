# 测试核心概念
## 为什么我们前端要学测试？
#### 1⃣️正确性：测试代码可以验证代码的正确性，在上线前做到心里有底
#### 2⃣️自动化：当然手工也可以测试，通过console可以打印出内部信息，但是这是一次性的事情，下次测试还需要从头来过，效率不能得到保证。通过编写测试用例，可以做到一次编写，多次运行
#### 3⃣️解释性：测试用例用于测试接口、模块的重要性，那么在测试用例中就会涉及如何使用这些api，其他开发人员如果要使用这些api，那阅读测试用例是一种很好的途径，有时比文档说明更清晰。
#### 4⃣️驱动开发：指导设计：代码被测试的前提是代码本身的可测试性，那么要保证代码的可测试性，就需要在开发中注意api的设计，TDD将测试前移就是起到这么一个作用
#### 5⃣️保证重构：互联网行业产品迭代速度很快，迭代后必然存在代码重构的过程，那么怎么才能保证重构代码的质量呢？如果有了测试用例做后盾，就可以大胆的进行重构
###### **开发人员简称：**
		1.QA----测试（‘一顿操作猛如虎，一问工资两千五’😄）
		2.FE----前端
		3.RD----后端
		4.OP----运维
		5.DB----数据库
## 1.单元测试
1⃣️目的：单元测试能让开发者明确知道代码结果
2⃣️原则：单一职责、接口抽象、层次分离
3⃣️断言库：保证最小单元是否正常运行检测方法
4⃣️测试风格：测试驱动开发和行为驱动开发均是敏捷开发方法论。
**TDD(测试驱动开发)：关注所有的功能是否被实现（每一个功能必须有对应的测试用例），suite配合test利用assert（‘tobi’ == user,name）；**
**BDD(行为驱动开发)：关注整体行为是否符合整体预期，编写的每一行代码都有目的的提供一个全面的测试用例集。 expact/should.describe配合it利用自然语言expect（1）. toEqual(fn())执行结果**
5⃣️单元测试框架
**主要用的就是chai.js(双模)和Jasmine.js(单模)这两种框架，国内主要是BDD模式，所以用jesmine.js**
![](https://www.showdoc.cc/server/api/common/visitfile/sign/2db07202cc9c4740c89644aa0764968e?showdoc=.jpg)
6⃣️单元测试运行流程(生命周期)
##### 1.before 单个测试用例（it）开始前
##### 2.beforeEach 每一个测试用例开始前
##### 3.it 定义测试用例  并 利用断言库进行设置 chai 如：expect(x).to.equal(true)；异步mocha
##### 4.以上专业术语叫mock 
**before---->beforeEach----> it ---->after---->afterEach
7⃣️自动化单元测试
###### 1.karma  自动化runner集成PhantomJS无刷新
###### 2.npm install -g karma
###### 3.npm karma-cli --save-dev
###### 4.npm install karma-chrom-launcher --save-dev
###### 5.npm install   karma-phantomjs-launcher --save-dev
###### 6.npm install karma-mocha --save-dev
###### 7.npm install karma-chai --save-dev
8⃣️开始写测试代码
###### 1.使用npm    cnpm（很坑）     yarn等装包工具  注意：mac要用sudo
###### 2.cd Desktop ---->   cd sch-qa
###### 3.npm init -y    直接确定本地一个包  会生成一个package.json文件
###### 4.在sch-qa文件夹内建一个test文件夹（试管），在test内建一个unit文件夹，在unit文件夹里新建一个index.js文件和一个index.spec.js（测试文件）
**注意：**
`index.js里最好不要写es6的语法，会报错，phantomjs对es6支持不是很好`

`index.spec.js里的代码如下：describe("测试基本函数的API",function(){
    it("+1函数的应用",function(){
        expect(window.add(1)).toBe(2);
    })
});`

######5.yarn常用命令
![](https://www.showdoc.cc/server/api/common/visitfile/sign/d0d160fce744ece95dca44607f44ecd3?showdoc=.jpg)
**yarn比npm的优势：yarn是离线缓存，如果是装过的包再装的话，基本秒装**
###### 6.--save-dev是开发阶段用的包        --save是上线时候用的包（重点！）
###### 7.npm login 登陆    npm publish    发布包    
###### 8.使用yarn add karma --dev   先装karma  这是装在本地  只能在本地跑
###### 9.使用yarn  global add karma-cli    是把包装在了全局  只能在全局跑
---------------------------------------------------------------
###### 10.进入sch-qa文件夹   执行karma init  在watch的时候选择no
###### 11.package.json文件中的scripts中加   “unit”:"karma start"
###### 12.对karma.conf.js进行配置
`1.files:['./test/unit/**/*.js',
      './test/unit/**/*.spec.js']`这是配置要测试的文件
	  
`2.singleRun：true`
在这里要改成true，因为用的是无头浏览器（无界面浏览器）

###### 13.进入sch-qa中  执行 npm run unit
###### 14.运行npm install karma-jasmine  jasmine-core --save-dev   装jesmine和jesmine-core
###### 15.运行 npm install karma-phantomjs-launcher --save-dev  
在14和15步的时候会报错，就是因为在index中写了es6的语法 ！！！！
###### 16.运行 yarn add karma-coverage --dev    是用来做代码的覆盖率检查的  就是为了防止我们写了多个分支语句的时候漏掉其中一个没有测试  但是也会成功
###### 17.在karma.conf.js中配置
`1.配置reporters：  reporters: ['progress', 'coverage']，加上覆盖率的检查`

`2.配置preprocessors: 'src/**/*.js': ['coverage']，在处理的时候，指定哪个文件要加检查`

`3.配置coverageReporter：coverageReporter: {
      type : 'html',
      dir : '.docs/coverage/'
    }，指定生成的报表的路径，一般放在当前目录的docs里`
**test和docs目录会伴随着整个项目**
---------------------------------------------------------------------
#e2e
###### 1.去npmjs去找selenium-webdriver 然后安装   npm install  selenium-webdriver 
###### 2.在package.json中的scripts中加入"e2e":"node ./test/e2e/baidu.spec.js"
###### 3.去官网下载火狐的驱动   然后放在项目根目录中  解压 
###### 4.运行  npm run e2e
---------------------------------------------------------------------
**rize.js来写单测**
###### 1.运行 npm install --save-dev puppeteer rize
###### 2.运行  yarn add --dev puppeteer rize
###### 3.在git.spec.js中加入
`const Rize = require('rize');
const rize = new Rize();`
###### 4.运行  node ./test/e2e/git.spec.js
## 2.性能测试
## 3.安全测试
## 4.功能测试