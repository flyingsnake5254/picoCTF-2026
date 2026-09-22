- [一、Git 基本流程](#一git-基本流程)
- [二、首次設定](#二首次設定)
- [三、建立或下載儲存庫](#三建立或下載儲存庫)
	- [將目前資料夾變成 Git 儲存庫](#將目前資料夾變成-git-儲存庫)
	- [下載現有儲存庫](#下載現有儲存庫)
- [四、查看目前狀態](#四查看目前狀態)
- [五、加入並提交修改](#五加入並提交修改)
- [六、查看提交紀錄](#六查看提交紀錄)
- [七、分支基本操作](#七分支基本操作)
- [八、連接 GitHub 等遠端儲存庫](#八連接-github-等遠端儲存庫)
- [九、取得遠端更新](#九取得遠端更新)
- [十、復原修改](#十復原修改)
	- [放棄尚未加入暫存區的修改](#放棄尚未加入暫存區的修改)
	- [將檔案移出暫存區，但保留修改](#將檔案移出暫存區但保留修改)
	- [修改最近一次提交訊息](#修改最近一次提交訊息)
	- [安全地撤銷某次提交](#安全地撤銷某次提交)
- [十一、暂存尚未完成的修改](#十一暂存尚未完成的修改)
- [十二、忽略不應提交的檔案](#十二忽略不應提交的檔案)
- [十三、完整操作範例](#十三完整操作範例)


## 一、Git 基本流程

最常使用的工作流程是：

```text
修改檔案 → git add → git commit → git push
```

- 工作目錄：正在編輯的檔案
- 暫存區：準備提交的修改
- 本機儲存庫：已建立的版本紀錄
- 遠端儲存庫：GitHub、GitLab 等伺服器上的儲存庫

## 二、首次設定

設定提交紀錄中顯示的姓名與信箱：

```bash
git config --global user.name "你的名字"
git config --global user.email "你的信箱"
```

查看設定：

```bash
git config --global --list
```

## 三、建立或下載儲存庫

### 將目前資料夾變成 Git 儲存庫

```bash
mkdir my-project
cd my-project
git init
```

### 下載現有儲存庫

```bash
git clone https://github.com/user/project.git
cd project
```

## 四、查看目前狀態

```bash
git status
```

這是最常使用的指令之一，可以查看：

- 哪些檔案被修改
- 哪些檔案尚未追蹤
- 哪些修改已加入暫存區
- 目前所在分支

查看尚未加入暫存區的修改：

```bash
git diff
```

查看已加入暫存區的修改：

```bash
git diff --staged
```

## 五、加入並提交修改

建立或修改檔案後，先加入暫存區：

```bash
git add filename.py
```

加入所有修改：

```bash
git add .
```

建立一筆版本紀錄：

```bash
git commit -m "新增登入功能"
```

建議提交訊息描述「這次修改做了什麼」，例如：

```bash
git commit -m "fix: 修正密碼驗證錯誤"
git commit -m "feat: 新增使用者登入功能"
git commit -m "docs: 更新安裝說明"
```

## 六、查看提交紀錄

完整紀錄：

```bash
git log
```

簡短顯示：

```bash
git log --oneline
```

以圖形方式顯示分支：

```bash
git log --oneline --graph --decorate --all
```

查看某次提交修改了什麼：

```bash
git show 提交ID
```

例如：

```bash
git show a1b2c3d
```

## 七、分支基本操作

分支可以讓你獨立開發功能，不影響主要版本。

查看分支：

```bash
git branch
```

建立分支：

```bash
git branch feature-login
```

切換分支：

```bash
git switch feature-login
```

建立並立即切換：

```bash
git switch -c feature-login
```

舊版 Git 也常看到：

```bash
git checkout -b feature-login
```

將功能分支合併到主要分支：

```bash
git switch main
git merge feature-login
```

刪除已完成的本機分支：

```bash
git branch -d feature-login
```

## 八、連接 GitHub 等遠端儲存庫

查看遠端設定：

```bash
git remote -v
```

加入遠端儲存庫：

```bash
git remote add origin https://github.com/user/project.git
```

第一次上傳並設定追蹤關係：

```bash
git push -u origin main
```

之後通常只需要：

```bash
git push
```

如果分支名稱是 `master`：

```bash
git push -u origin master
```

`main` 和 `master` 都可能是主要分支名稱，可以用以下指令確認：

```bash
git branch
```

## 九、取得遠端更新

下載並合併遠端修改：

```bash
git pull
```

它大致相當於：

```bash
git fetch
git merge
```

只下載遠端資訊、不立刻合併：

```bash
git fetch
```

多人協作時，通常推送前先執行：

```bash
git pull
git push
```

如果 `pull` 發生衝突，需要先手動解決衝突，再執行：

```bash
git add .
git commit
git push
```

## 十、復原修改

### 放棄尚未加入暫存區的修改

```bash
git restore filename.py
```

這會直接丟棄修改，使用前要確認。

### 將檔案移出暫存區，但保留修改

```bash
git restore --staged filename.py
```

### 修改最近一次提交訊息

```bash
git commit --amend -m "新的提交訊息"
```

如果提交已經推送到共享儲存庫，不建議隨意使用 `--amend`。

### 安全地撤銷某次提交

```bash
git revert 提交ID
```

`revert` 會建立一筆新的提交來抵消舊提交，適合已經推送的版本。

## 十一、暂存尚未完成的修改

臨時保存目前修改：

```bash
git stash
```

查看暂存清單：

```bash
git stash list
```

恢復最近一次暂存：

```bash
git stash pop
```

## 十二、忽略不應提交的檔案

在專案根目錄建立 `.gitignore`：

```gitignore
# Python
__pycache__/
*.pyc
.venv/

# 編輯器
.vscode/
.idea/

# 環境與機密資料
.env

# 編譯產物
build/
dist/
```

不要將密碼、API Key、私鑰等機密資料提交至 Git。

如果檔案已經被 Git 追蹤，加入 `.gitignore` 並不會自動停止追蹤，可以執行：

```bash
git rm --cached .env
git commit -m "停止追蹤環境設定檔"
```

## 十三、完整操作範例

```bash
# 下載專案
git clone https://github.com/user/project.git
cd project

# 建立功能分支
git switch -c feature-login

# 修改程式後查看狀態
git status
git diff

# 建立提交
git add .
git commit -m "feat: 新增登入功能"

# 上傳功能分支
git push -u origin feature-login
```

之後可以在 GitHub 建立 Pull Request，審查完成後再合併進 `main`。

最需要先熟悉的指令是：

```bash
git status
git diff
git add .
git commit -m "訊息"
git log --oneline
git switch
git pull
git push
```