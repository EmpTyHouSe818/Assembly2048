## 环境：

* 系统：windows系统，
* 编译工具：MASM32
* IDE: RadASM v2.2.2.3

## 函數主要功能介紹：

* 已完成主遊戲介面的設計及生成, 以及是遊戲里需要用到的一些功能函數, 以下為目前已編寫的函數及其功能介紹。
### 1. WinMain                 PROTO :DWORD,:DWORD,:DWORD,:DWORD
##### 程序入口點函數, 當中的四個參數為：
* hInstance 稱為“實例句柄”或“模塊句柄”。操作系統使用此值在內存中加載可執行文件時標識可執行文件 (EXE) 。某些Windows函數需要實例句柄，例如加載圖標或位圖。
* hPrevInstance 沒有意義。它在 16 位Windows中使用，但現在始終為零。
* pCmdLine 包含命令行參數作為 Unicode 字符串。
* nCmdShow 是一個標誌，指示主應用程序窗口是最小化、最大化還是正常顯示。
### 2. WndProc                 PROTO :DWORD,:DWORD,:DWORD,:DWORD
* 窗口過程函數, 用於攔截并處理系統消息和自定義消息, 也是以下大多數函數調用的入口點。關於系統消息的產生時機及處理手法請參考MSDN。
### 3. OnBlockReset            PROTO :HDC
* 在OnBlockInit中調用，用於清除當前所有遊戲記錄，同時還原遊戲界面到沒有方塊存在。
### 4. OnDraw					PROTO :HWND,:HDC
* 在收到窗口消息WM_PAINT時調用。
* 繪畫遊戲的LOGO, 16*16方格和顯示分數的部份, 采用了自適應窗口大小的繪畫手法, 當用戶調整窗口大小時, LOGO, 16x16方格和顯示分數的部份的位置會相應作出調整, 若當前窗口大小不足以容納以上所有元件, 則把LOGO舍棄, 只顯示16x16方格以及顯示分數的部份。
### 5. GetOneRandBlock         PROTO
* 在OnBlockInit中和移動了方塊且判定當前遊戲可繼續進行時調用，隨機在未有方塊的方格位生成一個2或4的方塊。
###  6. OnBlockInit				PROTO 
* 在每次開始新遊戲時最先調用，用於初始化，其內容包括生成2個隨機方塊，載入所有可能顯示的方塊資源，取得各個方格的句柄。
###  7. OnBlockUpdate			PROTO 
* 在OnBlockInit後調用，以及每一次用戶進行移動後調用，用於更新所有方格展示的的方塊內容。
###  8. ChangeBlockResource		PROTO :DWORD
* 在OnBlockUpdate中調用，用於取得當前要放入方格的方塊資源句柄。
###  9. DetermineBlockHandle	PROTO
* 在OnBlockUpdate中調用，用於把ChangeBlockResource中取得的方塊資源句柄放進某特定方格中，從而實現更新方格展示的的方塊內容。
###  10. OnScoreUpdate			PROTO :HWND
* 在分數出現變化時調用。
###  11. OnKeydown				PROTO :HWND,:UINT,:WPARAM,:LPARAM
* 遊戲的主核心邏輯模塊，用於判定當前用戶按下了哪一個鍵，并据此作出相應的動作。
* 未實現。
	
## 一些調用上述函數的例子：
* 包括更新方塊顯示, 更新分數顯示, 輸出最後遊戲的結果, 这些都暫時放在OPEN, SAVE中, 以作為例子給大家參考,
* 切記在完成OPEN, SAVE(即存檔讀檔功能)後把我這些例子都删去。
	
## 目前已知的BUG：
####  存放16x16方格數据的數組BLOCK貌似在讀或寫的時候出現了問題, 導致寫入數據時會出現錯誤, 这個請你們自行修改,
#### 同時, 由於顯示部份也是根據該數組的內容來顯示, 故進行修改後請隨便把用到了这個數組的部份進行修改。

## 注意事項：
#### 在代碼中絕對不要有中文，注釋也最好不要用，你們可憐的組員我由於用的電腦配置和你們不同，我會亂碼的。
#### 目前進行新遊戲前的用戶名稱讀取寫口，以及排行榜窗口还沒有實現，有哪位可以幫忙做做的接下來3天我要做我別的作業了嗚呼。


## 2022/5/25 19:00 
## record.asm函數主要功能介紹
### 1. InitDataBase proto
* 功能：加载sqlite3.dll，完成sqlite数据库的初始化工作
### 2. saveGame proto :dword, :dword, :dword
* 功能：将用户名称、当前游戏分数、当前游戏状态保存至数据库，必要时更新数据库中用户最高分
* 参数：
    * offset BLOCK
    * 用户名称的地址
    * 用户分数的地址
### 3. loadGame proto :dword
* 功能：从数据库中加载用户游戏分数、历史最高分数、游戏状态至num_score, num_best_score, BLOCK。
* 参数：
    * 用户名称的地址
### 4. updateBestByName proto :dword, :dword
* 功能：根据传入的名称、分数更新数据库中的用户历史最高分
* 参数：
    * 用户名称地址
    * 得分地址
### 5. getBestByName proto :dword
* 功能：根据用户名称得到用户历史最高分
* 参数：
    * 用户名称地址
* 返回值：
    此函数有两个返回值。eax中为历史最高分，ebx为错误信息。若用户是首次游戏（无历史最高分），则ebx = 1. 否则ebx = 0.

### 6. prepareRankInfo proc
* 功能：从数据库中读出排名前5的用户名称和最高分，填入rank_info1-rank_info5
### 7. dword2str proto :dword, :dword
* 功能：将dword类型转换为byte（str）类型
* 参数：
    * dword数据地址
    * byte数据地址
* 说明：第二个参数为存放str的地址，要求有足够的空间存放转换的字符串
### 8. str2dword proto :dword, :dword
* 功能：将byte（str）类型转换为dword类型
* 参数：
    * byte数据地址
    * dword数据地址
    

## 模块化编程方式
* RadASM支持汇编语言的模块化编程，允许写成多个.asm文件并链接。
* 如果想要写成多个.asm文件，需要在[工程->工程选项]中勾选[编译模块]
* 添加一个模块的方法是在Resources下[右键->新建->模块]
* 单独编译一个模块的方法是[构建->编译模块]，此时会生成对应的.obj文件
* 然后在Assembly文件夹下导入目标文件.obj
* 最后编译主程序的.asm文件，即可将.obj文件链接


## 更新说明
* 主游戏窗口菜单中的File项下增加了Name选项，点击弹出设置名称的窗口

## 仍存在的BUG
* 在关闭“设置名称”窗口或关闭“排行榜”窗口后，点击主游戏窗口后主游戏窗口会最小化

## 仍待优化的地方
* 用户名称输入界面和排行榜太丑了
