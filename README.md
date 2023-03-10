# 0到1，開發一個獨立的跨平台系統 

# 前言
在一次的因緣下，Linkin有位公司負責人跟我面談，公司本身產品是做安防產業，
主要產品就是監控攝影機，還有人臉辨識、門禁等等，
而這次的面談主要是希望我能協助軟體技術的服務，
原先想說還好，沒想到是一連串的難度挑戰，所以就想把這過程記錄下來。

# 需求
一開始原先以為是只是做個軟體服務，沒想到在理解後，幾乎就是一個系統，
包含帳號登入、角色權限、監控排程、地圖監控等等，
主要的情況是這樣，公司主要業務是設備並且與外部軟體廠商配合來開發產品進行販售，
內部並沒有任何的程式設計師，但是有設備相關技術的老闆，
所以這個需求，就從這個監控設備技術的老闆來跟我對接。

可是因為內部並沒有程式設計的人，這份規格看起來比較像是**「客戶需求企劃書」**，
例如，排程需求，文件上面就會寫 **「介面與功能類似Windows的排程，但是要有我們自己要的邏輯」**
這樣雖然是有一些概念，但是很多細節、操作其實都沒有很詳細，所以得重新整理。

![image](https://github.com/MarlowChen/solo-dev-sys-record/blob/main/img/001.png)


# 技術背景
在這以前，我最早是做UIUX網頁設計開始，後來學半年Java後投入職場寫了四年多的Java，
過程中都有接觸前端，寫Java工作的後期學習Angular與Vue並導入到專案內，
後來轉職前端設計寫React，然後因為接觸區塊鏈與加密貨幣技術，離職接案賺寫智能合約與自己架設NFT商城(使用Nodejs與NextJs)，直到加密貨幣震盪後，才到這間公司。

換句話說，在這之前我並沒有接觸過監控的東西，也沒碰過串流，
幾份工作大部分都是Coding，只有少部分的系統設計經驗。

然後部屬的話也是用過Aws，後面都使用Gcp，也用過各種不同可以上架的網站，
如vercel、fly.io、heroku等等，後面就比較常使用Docker，但也只是拿來上架而已。

# 自訂開發SOP

不管如何，只能硬著頭皮上了，就當作是一種挑戰，
根據之前系統開發的經歷，我也清楚一個系統需要很多角色，
但是，這個系統不算大，雖然這樣講，細節還是相當多的。
如果一個人要完成開發，還是得在做一些功課，
請教過很多業界資深退休的IT總經理與前輩，就盡可能的整理出SOP，

- 選擇技術
- 整理規格書
- 設計資料表
- 設計程式結構
- 開發程式

比較特別的地方是，我在整理規格書到開發程式這一段，是一個小型循環，

![image](https://github.com/MarlowChen/solo-dev-sys-record/blob/main/img/002.png)

以下的舉例只是用這個SOP做簡單的舉例，但詳情其實更複雜。

## 一 . 選擇技術
起初基於穩定技術我考量我寫比較久的Java，但是，這必須要考量現況的問題
- 這個系統雖不算小，但也不算大，要考慮可擴充性
- 我只有一個人

這兩件事情，會大幅影響到的就是**開發成本，也就是時間與人力，這對於系統來說並不利**，
所以我思考後，就用我寫過的NFT後台來加以改良使用，
當時是做前後端分離，使用NextJS與NodeJS，並且用NoSql的MongoDB。

但這個系統需要有一定的穩定度與資料結構設計，加上我只有一個人開發
所以**基本原則是要讓前後端能夠非常好綁定**，
另外，我不能花太多時間處理**「UIUX」**，就是盡可能地讓產品可以**減少開發成本，提早上線**。
因為前端主流基本上就是Javascript相關，再加上函式庫(開發資源)夠多，
就這樣定了前後端語言，而資料庫，為了穩定性與維護，在Mysql與PostgreSQL花了一些時間比較，
最後選擇了PostgreSQL。

- 主要語言‧Javascript
- 資料庫‧PostgreSQL

在這邊的技術，算是一種**戰術考量**，但是，後續還是有很多需要處理的。

註解 ： 這邊沒提到細節，後面會詳細說明


## 二 . 整理規格書
雖然需求上大致看得懂，但是因為如前面所說的，因為都是參考的，所以看起來是很零散的，
系統開發是需要較有邏輯的規範的，所以必須先做一個整理。
譬如登入，這是企劃書從其他系統參考來的，但以規格書不能這麼零散，但我也沒有時間規劃規格書，
所以在時間允許下的做法，就是參考現在主流的設計方式
於是原先是長這樣

![image](https://github.com/MarlowChen/solo-dev-sys-record/blob/main/img/003.png)

變成這樣

![image](https://github.com/MarlowChen/solo-dev-sys-record/blob/main/img/004.png)

主要是說，把原先零散的狀況做一個系統關聯，因為是自己開發，不需完整寫成規格書，
再來因為我本身還是有些介面底子，加上是系統，就不需要太過於在設計界面，
上述只是用登入來舉例，整個系統大致上都是需要這樣關聯。

但是上述只是最簡單的登錄而以，畢竟這是一個**「監控系統」**，後面的攝影機、排程還有地圖都會關聯進去，到時也是會非常龐大的。

## 三 . 設計資料表
系統寫久了，對於資料表多少有一點概念，但是並沒有真的從無到有設計資料表過，
所以我就開始上網找各種登錄權限資料表設計，怎麼樣才能做出好的資料表，
最後就做出這樣的權限設計，
當然這也不包含前面提到的SMS與Email認證，單純以可以**高度擴充的權限設計**，
後面的所有資料表，幾乎都會與這個些資料表關聯。

例如，我這個監控攝影機排程，就會跟攝影機還有排成細節設定都息息相關，
這還不包含前面權限綁定，如果加上去，整個設計也是有些複雜的。

## 四 . 設計程式結構
如前面所述，後來在經過思考後，使用NextJs提供的Custom Server範例進行開發，
https://github.com/vercel/next.js/tree/canary/examples/custom-server
而且只使用Js，不使用Typescript，這是一些套件相容問題的考量。

所以大致上比較主要的Lib是
- NextJs. 前端框架
- React Material-ui. UIUX
- YupJs. 更快速驗證表單
- ExpressJs. 針對Api更方便的Lib
- PassportJs. 用來驗證會員資訊
- SequelizeJs. 資料庫Orm

但是這只是初期的構想，中間會一直在做調整，Lib也會依據場景而使用，
例如我要做地圖，就會使用到LeafletJs，要做到天氣，就會需要使用第三方的Api，
需要讀取影片就使用VideoJs等等，
這邊就解釋為什麼我要使用Js作為我主要開發的語言了，
在使用過程，可以很容易的使用各種不同資源。

## 五 . 開發程式
原先規劃大概就是確立規格然後調整資料表，調整結構，
大概就是這樣進行規劃與開發，大致上在過去有接觸過軟體開發的部分其實都是小問題，
畢竟大致上技術其實就是這樣，
但是真正的問題不再於程式開發的技術，而是更複雜的問題。
所以我整理了過程中最困難的部分，也是這篇記錄的重點

#### 真正的問題，開發遇到的瓶頸

1.串流技術問題
3.排程商務邏輯
2.地圖與攝影機結合
4.部屬與產品發布

## 串流技術問題
前面沒有提到，因為是監控攝影機，這表示需要理解串流相關的技術，
簡單解釋，只要是攝影機，就會有這樣的Rtsp串流，需要一個媒體伺服器來解析串流，
在把它變成網頁可以瀏覽的影片。
換句話說，需要有一個將攝影機串流做轉換的伺服器。
串流共用的轉換方式，基本上都是依賴FFMPEG這個軟體，幾乎外面所有串流伺服器大多都會與此去做設計。
一開始開發的時候，原先公司已經有一個做法，
就是手動寫一個腳本，然後呼叫FFMPEG去將串流做轉換輸出到資料夾，然後每過幾秒鐘把串流清掉重生，
這樣就可以做出一個小的串流伺服器。

問題是，這樣就至少有兩個問題，
- 每一次新增攝影機都要手動新增
- 攝影機如果中間斷線不會自動重連

這還有後續很多擴充和效能問題等等，尤其是攝影機斷線的問題，後來在我來之前，
他們直接寫一個15分鐘就重啟的方式，只是也不是好的解法。

所以我花了一段時間先理解串流原理後，
我並沒有打算自己開發伺服器，而是使用現成的伺服器，因為當初我使用這個結構就是為了擴充，
最後為了讓相容性高，就使用了NodeJs支援的**Node-Media-Server**
https://github.com/illuspas/Node-Media-Server

雖然中間幾次嘗試各種不同的伺服器，包含了Golang與Python都有去找，
可是一開始，**我並沒有考慮到效能，單純以相容性為考慮目標**，
而NodeMediaServer是可以直接包在NodeJs的Server內，在啟動時一併啟動的。

起初其實沒有太大的問題，可是..
攝影機斷線後，這個NodeMediaServer卻一樣無法自動重連，重新閱讀這個Lib，去看了原生程式碼，
也看到很多人在Isssue中提問，這似乎是無解的，
還有一些更大的問題，前面提到效能，原先沒考慮到，到了開發中後期，
老闆就問到**「我最多可以裝幾支攝影機上去」**，
根據我的測試，**「沒錯我似乎砸鍋了，這個效能上還是有些問題」**
我當時想到的做法，要不就是換掉，要不就是直接改寫Source Code。

但是，基於時間考量，在市場中做串流的前輩指導下，最後我找到了一個更好的開源Server，
**ZLMediaKit**
https://github.com/ZLMediaKit/ZLMediaKit
老實說，寫到這邊，我還是要強調，比起我一個人在顆輪子，我認為這些大神的東西才是真的厲害，
使用C++效能相當優異，測試後攝影機也會重啟，Api完整度又相當高，就決定使用，
但是，前面有提到我希望可以相容，該怎麼做呢？那又是另外一個問題了。

![image](https://github.com/MarlowChen/solo-dev-sys-record/blob/main/img/006.png)

## 排程商務邏輯

這個系統，最重要的核心功能，就是縮時排程，
簡單解釋就是：讓攝影機能夠做到像是縮時攝影的功能，可以每隔多久拍一張照片，
最後系統會把它組成一組完整的影片。
而這個功能很好在工地上使用，譬如建造房子的時候，如果可以利用監視器縮時攝影幾年，組出來的影片會非常有效益。

可是講起來簡單，這個功能我改寫非常多次，
最基本的邏輯是這樣的，可以設定一段時間，然後可以決定隔幾天、隔幾周、幾個月去拍攝，
每隔多久拍攝一次，每多久要產生一段影片，
然後還有一個問題，這個功能最重要的還要加入硬碟空間考慮，所以每隔多久要刪除照片，這類都要設定。

起初我也是想的簡單，就想說我只要寫個排程就可以，依據設定檔去產生排程就可以。
但光這一點就是一個錯誤判斷，隨便舉例就有幾個Bug

- 攝影機斷線怎麼辦？
- 系統斷線怎麼辦？
- 遇到跨年問題怎麼辦？
- 中間如果有人亂動設定檔怎麼辦？

這個功能最大的問題在於，所有設計都息息相關，
所以光一開始設計，我就花兩三天在邏輯的考量上，最後決定基本邏輯是這樣

「每一次在做快拍排程的時候，去檢查下一次排程要不要做」

這個基本邏輯很重要，後來慶幸一開始有想到這個原則，
因為，例如攝影機掛掉或者系統掛掉，重啟後，我只要檢查現在做到哪個排程就可以了，
只是每一次排程都要做紀錄就是了。

當然過程中也是有很多問題，譬如寫錯排程一口氣不小心一個晚上塞了幾億筆資料，
或者每個月哪幾號做的時候，沒判斷好一個月有幾天之類，
還有，**NodeJS本身的多執行緒比較新，因為排程太多，導致其中一個排程掛掉就整個掛掉，**
**所以最後用子程序處理才解掉，這也是當初沒想到的大坑**，
只能說沒想像的簡單，但是還好最終也是克服了。

        async doNextSchedule(nowTime, service, defaultRandomUUID) {
            const doNext = service.doNextSchedule;
            const saveTime = new Date(nowTime.getTime());
            const next = await service.getNextScheduleTime(saveTime);
            //下個預計時間需要扣除10秒，要延遲拍照用
            const nextTime = next && new Date(next.nextTime.getTime())
            //如果下個時間已經過去，則本次時間不加入排程，直接做下次的時間
            const nextCanNotDo = new Date().getTime() >= (saveTime.getTime() + 10000);
            const status = nextCanNotDo ? "MISS" : "PREPARE";
            const randomUUID = defaultRandomUUID ? defaultRandomUUID : uuidv4();
            if (!defaultRandomUUID && !nextCanNotDo) {
                //下一張圖片index先++
                for (let key in service.cameraScheduleObj) {
                    service.cameraScheduleObj[key].imageIndex++;
                }
            }
            if (!defaultRandomUUID) {
                await service.createScheduleToDB(randomUUID, "IMAGE", saveTime, status)
            }
            if ((!next || next.isMerge) && !defaultRandomUUID) {
                await service.createScheduleToDB(randomUUID, "VIDEO", saveTime, status)
            }
            if (nextCanNotDo && nextTime) {
                service.doNextSchedule(new Date(nextTime), service, null);
                return;
            }
            const cameraCache = service.doCameraCache;
            try {
                schedule.scheduleJob(randomUUID, nowTime, async function () {
                    try {
                        await cameraCache(randomUUID, service, next);
                        if (next) {
                            const nextTime = new Date(next.nextTime.getTime())
                            doNext(new Date(nextTime), service, null);
                        }
                    } catch (error) {
                        console.log(error)
                    }
                });
            } catch (error) {
                console.log(error)
            }
        }
## 地圖與攝影機結合
這個專案還有一個重點，就是可以與地圖結合，
單看地圖其實沒什麼問題，但是一開始原先使用Google地圖是要付費的，
經過討論後，使用開源的地圖OpenStreetMap，
但這並不像Google地圖那麼容易使用，後來找到leafletJs，這是一個開源Lib，
除了要解決SSR的相容外，大致上問題不大，
可是這個需求並不是只有把地圖載入而已，這個功能是管理員可以在地圖上設定座標，
讓外面的人可以看到監控在哪個位置，跟地圖做綁定，會讓人更清楚這台監控在哪個位置上。
麻煩的是，因為地圖是需要被刷新的，加上圖標與監視器控制就更難了，
還好就是在熟悉Js的情況下，終於把需求完成。

![image](https://github.com/MarlowChen/solo-dev-sys-record/blob/main/img/camera.gif)


# 重新整理系統
這是開發後遇到的問題，也是最麻煩的，其實起初沒想到這會是一個問題，
原因是這樣，一開始想說可以上到雲端，我用同樣的架構也都上架幾次了，
想說這應該沒問題，沒想到這時候，公司這邊又討論需要幾個問題了。

- 需要做試用產品，所以要做產品授權
- 需要增加離線地圖，可以斷線使用
- 能夠讓技術人員直接到客戶那邊安裝
- 攝影機掛掉還是沒有辦法重啟

這些事情，在一開始其實並沒有考慮到，
因為原先我是做了「上雲端的設計方式」，原先並沒有考量讓其他人去部屬，
另外，前面提到的媒體伺服器，其實原先一直都是使用NodeMediaServer，每隔一段時間去檢查，
沒想到其實是在最後驗收要部屬的時候，發現這個方法行不通，所以才換掉的。

也就是最後在要部屬之前，這些問題都要解決掉才可以，
但頭痛的事情是，我並沒有放在一開始的計畫內，
這會影響到幾個狀況

- 程式語言: NodeJS並不適合做授權，也不適合做加密
- 程式結構: 因為MediaServer與離線地圖，結構要重弄
- 部屬作法: 當大家都能安裝的時候，那就需要讓做法更簡單

寫到這邊，總不能這時候全部打掉，所以就一步一步來解決這些問題。

原先使用的是NextJs + NodeJs開發，主要使用Custom Server，
而前面有提到，我原先這麼做是為了要讓整個系統保持一致性，
現在全部都要調整，就得先規劃幾件事情要執行：

1. Node Media Server換成ZLMediaKit
2. 離線地圖
3. 授權設計
4. 程式加密與打包


## Node Media Server換成ZLMediaKit
花了一些時間去看ZLMediaKit後，會覺得這個Media Server真的很強大，
可是使用了C++開發的，不能夠跟Js相容，但是我還是想要做到原先能夠讓系統一致性的原則，
後來看了文件，上面有提供Docker Image，這是一個很好的做法，
所以就花了一些時間，讓Server去由Docker啟動，
在依據文件把Rtsp串流丟過去，基本上測試後就沒有問題了。

## 離線地圖
地圖一般都會使用區塊渲染，也就是使用者到達那個區塊才會讀取那一塊地圖的樣子，
這麼做其實就是為了避免地圖圖包太大，
但是改成離線地圖，上網找了文件，做法其實就是預先把地圖包抓下來，
原先我也想的簡單，只是地圖包而已，我就測試一下用網路提供的軟體抓地圖，結果因為地圖是有放大縮小的，這並不是把同一張圖塊放大縮小而已，而是不同圖塊，結果因為這樣導致圖塊太多，抓到一半就當機。

後來才知道，離線地圖基本上就是架設一個本地的地圖Server即可，
最後也就起了一個Server就解決問題了。

## 授權設計
Media Server與離線地圖還算是小問題，網路上大多有答案，但是授權是一個大問題，
一般來說，這樣的做法其實一開始就要往**「應用系統來開發」**，
最常見的都是Java與C#，尤其是C#，因為Windows系統的關係，基本上大部分需要產品授權都會很常使用C#來開發，相容性相對高，
可是我已經使用NodeJs了，總不能這時候換語言，
而且現在程式大多使用網路授權了，基本上還是必須要朝這個方向解決才可以。

最後，經歷很多測試後，討論出一個方向，就是使用公鑰與私鑰的作法，
在程式裡面藏了私鑰，必須要有公鑰對應才可以，
而我的作法就是，在NodeJs的Server端去處理公鑰是否正確，所以等於是在程式內藏了一把私鑰，
理解程式結構就是前端的畫面與後端資料API，
所以我在Server這邊做了攔截加密後，就可以做到驗證機制，使用者必須要把公鑰加入進去軟體驗證才能使用，而且這是有有效時間的。

![image](https://github.com/MarlowChen/solo-dev-sys-record/blob/main/img/005.png)

## 程式加密與打包
雖然做了授權，但是很多人應該發現一個問題，我的SouceCode還是暴露的，
換句話說只要稍微懂Code還是輕鬆破解這個系統，
再加上前面說的，NodeJs本身並沒有辦法直接加密打包程式，
甚至在台灣NodeJs社群也有人有這個疑問，很多人都說乾脆把核心業務寫在Golang換過去就好，
而這件事情，也被列入Nodejs開發的討論，可見這是一個有關注度的問題，
[提倡NodeJS打包成單一執行檔](https://github.com/nodejs/node/issues/43432 "提倡NodeJS打包成單一執行檔")
但作為一個Jser一定還是想盡量做到，畢竟需求就是這樣，總不能說，語言開發者無解就是無解吧？
網路上有幾種解法。

- 加密
- 混淆
- 打包

稍微看了一下，基本上大部分的範例都有提到這三個，
先看一下我原先的結構，原先的結構，因為會把Server前後端放在一起，
所以要部屬基本上會把整個Node_Module包進去，就會產生非常肥大的狀況，
然後，加密與混淆，經過我測試，不管任何工具都**只能針對單一的Js檔案加密混淆**。
測過幾次，混淆與加密後檔案都會直接不能啟動。

所以我們能做的，就是**打包封裝**，目標就是將程式包成**「單一執行的檔案」**，
上網找了很久的文章，基本上也有很多人想要設法解決這個問題，
而最有名的工具就是Ncc、Pkg、node-packer，
原先覺得找到救星了，看到這幾個工具寫得很簡單，就來測試一下，
**結果發現這三個全都不能用，不是打包失敗，不然就是打包後檔案不能用**，

其實仔細想一下，我的結構本身撰寫就來的複雜，
我這個結構，包了一個FFMPEG在裡面，前端後端又綁在一起，套件又用非常多，外部的打包工具直接無解。

但總不能就這樣棄權，
繼續找尋答案，找到一個關鍵點，**NextJs可以輸出靜態網頁**，
意思就是我可以讓NextJS變成Html+單純的Js檔案，
輸出後發現這是可行的，但是，Api與Router是完全失效的，因為都叫做靜態網站了，當然不能動，
這時又想了一些辦法，
> 把NodeJs的Server改成原先渲染NextJs的方式，改成靜態網站渲染的方式
經歷過一番的波折後，終於改完，**因為最糟糕的是網路上沒有範例，只能以自己的經驗來做程式處理**，
但接下來加密還有打包的問題，我們的NodeJS還是自帶很肥的Server，
所以在回去看一下這些打包工具，終於找到曙光，而且後來發現，很多中國的大神也這樣幹，
看起來似乎是目前較佳的解法了，步驟基本上就是

1. 使用NextJs build export輸出靜態網站
2. 用Ncc將後端Server輸出編譯
3. 拷貝需要的檔案到打包後的資料夾(sequelize、ffmpeg)
4. 執行pkg將專案封裝成exe

這樣基本上就將程式給壓縮了，
然後將需要加密的資料都放在環境變數，大致上就完成這個加密與封裝，
**其實不能說這樣的方式是最佳加密，但可以確定，這樣的方式不好被破解**
**而且，移除NodeModule，加上壓縮，又改成靜態網站，效能可以說是最大化了！！**

# 統整結構與部屬--Docker
整理完每個部份之後，可以看到，除了軟體的前後端Server外，
還有一個Media Server和Map Server，
雖然，照原先考慮，都可以包入到同一個Server上，甚至變成一個Exe就可以執行，
但是，經過測試，以我們的程式結構，並不適合這樣做，主要是包太多第三方的東西，
在綜合考量下，我認為最佳的解法就是拆開，然後使用Docker在把整個結構包進Docker，
如此一來，就算技術人員要安裝，也是非常容易做到單機應用，
而且這樣的系統，還可以在未來做到**跨平台、高效能、高相容性**，算是我最後的解決手段。

![image](https://github.com/MarlowChen/solo-dev-sys-record/blob/main/img/007.png)

# 例外與重新來過
原先想說這樣的結構已經是我最後解決的方式，
沒想到，遇到了一個大問題，
**程式有一個功能，需要儲存在本地端的硬碟，但是Docker是虛擬的VM，無法訪問本地硬碟**，
這一個狀況幾乎讓所有結構崩虧一匱，因為上述都是以Docker為核心考量的。

- 探討解決Docker VM無法呼叫硬體的問題
- 打包後資源被咬住
- 上測試機時，多台攝影機無法使用問題
- 部屬與license的問題

所以從這邊來看，最終要解決這四個問題
## Docker VM呼叫外部硬碟
最初使用alpine linux來做為Docker的環境，所以最終用pkg壓縮成為一個完整的執行檔，
在包進Docker裡面。
原先以為這樣做萬無一失，直到測試到需要寫入硬碟時，發現一直寫不進去，
後來驚覺，因為VM機器是自己獨立，無法直接跟硬碟的OS溝通，
努力想辦法後，本來以為使用volume是一個曙光，經過艱辛萬苦的測試後，發現還是無解，
因為volume只能靜態綁定實體硬碟位置，但我的硬碟是可以程式動態設定的。

後來想了很久，只好放棄Docker，退一步直接用pkg跑exe，但如果不使用Docker，
那我還有一個串流Server要跑要怎麼辦？
原先想說，那就包進pkg就好了，所以找了想辦法將ZLMediaKit變成exe的方法，
然後透過程式呼叫這個串流server，原以為成功，但是pkg呼叫還是不行，
上網查了pkg討論區才知道，原來pkg根本不能把exe包進去再呼叫。
最終，只好把ZLMediaKit拿出來，外部再呼叫就好。

## 打包後資源被咬住
再最終成果後，跑下去又出現了一個問題，因為我的程式有寫排程，
然而**NodeJs本身是單執行緒**，雖然我有做了子排程，可是沒想象這麼簡單，
因為主要功能需要抓拍攝影機，但問題是如果攝影機被咬住，子排程是不會自己關閉的，
如果又在單執行緒的情況下，程式基本上就會被咬住不動，
而且，關鍵在於pkg的打包容易造成這個問題，很容易就咬住，
花了很多時間，最後決定換用nexe打包，並且改變一些程式寫法，終於解掉。

##上測試機時，多台攝影機無法使用問題
本機測試後，原以為萬無一失，至少攝影機看起來都很正常，
結果上了測試區，串流伺服器發生一堆問題，但這是一個奇怪的錯誤，
因為也是用第三方的串流Server，那肯定是看開發者那邊的討論區有沒有解，
[問題與作者回應](https://github.com/ZLMediaKit/ZLMediaKit/issues/2170 "問題與作者回應")
看起來，這個問題不是只有我遇到，換句話說，問題沒意外就出在於監控設備的串流，
除非我們換掉監控設備，否則此題無解(作者說可以換成其他協議，但其實一樣無解)。

所以想了好幾天，最後又只能退回NodeMediaServer，因為至少，那是接近用FFMPEG的概念，
可是我終究要解決串流斷線不能重連的問題，
最後，還好現在都是Open Source，再看過一輪這個原生Code後，直接改寫然後引入，解決問題。

## 部屬與license的問題
終於來到最後一關，上面給了一個問題，我販售產品之後，如果客人買一套之後一直複製怎麼辦？
老實說，在業界其實很常見這樣的狀況，在厲害的系統都有可能被破解，
但是對於程式人員來說，思維的點就在於「盡可能讓多數人無法破解即可」，
雖然我做了封裝的動作，但如果以使用角度，封裝只是不能修改，但還是可以部屬使用，
一開始，我做了一個公鑰與私鑰驗證，但這還是無解，一支公鑰還是可以部屬好幾個地方，所以，後來解法就是，綁定硬體系統+封裝+公鑰，因為系統內某些代碼是唯一值，這樣的方式就可以解掉蠻多問題的。

可是，這樣的狀況，就要重新調整封裝的內容，因為原先的設計方式，可以直接上Docker部屬到GCP，當重新調整後，封裝的檔案會牽涉到路徑的問題，也是耗費很多時間調整的。

# 結語心得與Q&A

**Q** **:** 使用NodeJs會不會有效能問題？

**A** **:** 我有思考過，上網查了文獻，其實很多社群也是使用NodeJS，Linkin就是一個代表，加上關鍵的封裝，其實整體效能是很好的，另外NodeJs與K8s相關引擎有良好的部屬，這也是效能影響之一。

**Q** **:** 有沒有安全疑慮？

**A** **:** 一般來說，以全端網路程式主要可能是PHP、Ruby、NodeJs常見，而我自己有使用過的是PHP，但這常常被詬病有被盜的問題，如Facebook，而相對的Linkin就少些，當然寫法有差，但論語言來看，目前並沒有安全問題。

**Q** **:** 最困難的問題

**A** **:** 其實這過程很多問題都是很困難的，光是pkg打包就發生很多狀況，不了解一些底層概念根本無法完成，還有打包後遇到nodejs單執行緒很容易卡死，這不單單只是程式，還牽涉到結構

**Q** **:** 這個專案技術的重點與特徵

**A** **:** 我確信這樣的結構目前是沒有看到的，因為參與nodejs在github上的討論，另外還要對前端有一定的熟悉度(nextjs的各種生命週期)，要說這個專案重點不是程式，是結構。

記錄到這邊就做個結束，其實這一個紀錄的重點主要不是在於**技術**，
而是自己對於程式設計的思維，

像我一開始從串流開始，因為不熟悉，就須要花大量的時間去找答案，
還要思考像是NextJs的撰寫方式，如我在一開始使用的是動態id，結果因為打包成靜態資源不支援，又得重改過，
到後面要做部屬的時候，整個結構需要打掉重作，
還要跑去看討論區有沒有比較正確的做法，沒有的話就得嘗試與參考大神的作法去找答案，算是刻苦克難。

當然，Code還真的不是寫得很漂亮，也重構很多次，還有其實也省略掉很多東西，
但是畢竟也是短時間內完成的，也是至少從無到有建置一個完整的系統了。

