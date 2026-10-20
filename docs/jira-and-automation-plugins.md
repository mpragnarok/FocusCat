# FocusCat 的兩個附帶外掛：「Jira 我的卡」與「自動化工作」

> 這份依 FocusCat 最新發行版 v0.6.18 的文件寫成。只想快速看懂，先讀[一頁摘要](jira-and-automation-plugins-summary.md)。

## FocusCat 是什麼

FocusCat 是 Mac 選單列上的番茄鐘。選單列上有一個小角色，點一下打開面板。面板只回答一件事：現在做哪一件。

**外掛**是一支小程式。FocusCat 定時叫它，它交回一份清單，清單裡的事就出現在面板上。正式版附兩支外掛：「Jira 我的卡」和「自動化工作」。兩支都不會自動裝上，要用的人自己按「安裝」。

## 取得與安裝

- 需要 macOS 13 以上，Apple Silicon 和 Intel 都能用。
- 到發行頁下載 `FocusCat-<版本>.zip`：<https://github.com/mpragnarok/FocusCat/releases/latest>
- 點兩下解壓縮，把 `FocusCat.app` 拖進「應用程式」。
- 第一次打開要按右鍵，選「打開」，不要點兩下。
- 系統說沒辦法確認這個 app 的話：打開「系統設定 → 隱私權與安全性」，往下捲到「安全性」，按「仍要打開」，輸入密碼，再開一次。這個步驟只有第一次要做。
- macOS 會擋下來，是因為這個 app 沒有付費的 Apple 開發者簽章，跟 app 本身有沒有問題無關。

## 「Jira 我的卡」

**它做什麼**：把指派給你、還沒做完的 Jira 卡帶進面板的「建議加進來」。預設每 5 分鐘查一次。從 Jira 來的卡，標題右邊有「Jira」小標籤。

**先準備 Jira 帳號資料**

1. 到 Atlassian 建一個 API token（API token 是給程式用的一串密碼）：`id.atlassian.com/manage-profile/security/api-tokens`，按「Create API token」。token 只顯示一次，先複製下來。
2. 打開家目錄的 `~/.zshenv`（沒有就新建），加三行**環境變數**（寫在設定檔裡、給程式讀的值）：
   - `JIRA_URL`：你們 Jira 的網址，要 `https://` 開頭
   - `JIRA_USERNAME`：你登入 Jira 用的 email
   - `JIRA_API_TOKEN`：剛才複製的 token
3. 結束 FocusCat 再打開。它只在打開時讀一次這三個值。

三行要寫在 `~/.zshenv`。寫在 `~/.zshrc` 的話，FocusCat 讀不到。帳號和 token 只交給這支附帶的 Jira 外掛，別的外掛拿不到。

**裝起來**

1. 打開「設定 → 外掛」，在「可以加的外掛」找到「Jira 我的卡」，按「安裝」。裝好後開關已經打開。
2. 點進「Jira 我的卡」，在「狀態」那一列按「試跑」。紅字消失就成功了。
3. 還看到「讀不到：Jira env 缺…」，代表三個環境變數沒讀到。回頭檢查第 2、3 步。

**可以設定的地方**（都在「Jira 我的卡」那一頁的「設定」）

- **收哪些狀態**：按「讀取」，從 Jira 讀出有哪些狀態，再勾選。沒勾過的話，只收「In Progress」。
- **不收哪些狀態**：沒設的話，只排除 Done。
- **建議名稱的格式**：填 `{卡號} {標題}`，建議就會寫成「DEMO-123 卡片標題」。空著按「存」，就回到只顯示標題。
- **放進建議**：關掉的話，外掛照常跑，只是卡不出現在建議清單。

**順便一提**：Jira 卡也能放進「在等」。「在等」放的是卡在等別人的事。FocusCat 每 5 分鐘看一次那張卡，卡一有變動，就跳通知「等的事有進展了」。

**FocusCat 不會改你的卡**：目前的發行版對 Jira 只讀不寫。

## 「自動化工作」

**它做什麼**：任何腳本只要寫一份**狀態檔**（描述工作跑到哪裡的 JSON 檔），那件工作就會出現在面板的「自動化」區。shell 腳本、排程工作、AI 背景工作都可以寫。外掛只讀檔，不執行檔案裡的任何東西。

**裝起來**

1. 打開「設定 → 外掛」，在「可以加的外掛」找到「自動化工作」，按「安裝」。裝好後會多一個**類別**叫「自動化」（每一件事都屬於一個類別）。這個類別的事不算專心時間，也不進建議。它們在面板上自己一區，輪到你時會通知。
2. 自己建資料夾，外掛不會幫你建：`~/Library/Application Support/FocusCat/automation/`
3. 讓腳本在跑的時候，把 `<代號>.json` 寫進這個資料夾，並且每隔一陣子重寫一次。

**每件工作有五種狀態**（`state` 欄位）

| state | 面板上 | 會通知你嗎 |
|---|---|---|
| `running` | 在跑 | 不會 |
| `queued` | 【排隊中】 | 不會 |
| `done` | 【做完了】 | 不會 |
| `needs_you` | 【要你處理】 | 會 |
| `failed` | 【失敗了】 | 會 |

`running` 或 `queued` 的工作，狀態檔太久沒更新時，面板會改標【可能卡住了】，也會通知你。「太久」是指超過檔裡 `stale_after_minutes` 寫的分鐘數。

通知只在工作「變成」要你處理、失敗、或可能卡住的那一刻跳一次。如果 FocusCat 第一次看到這件工作時，它就已經是這幾種狀態，就不跳通知。想關掉通知，到「設定 → 類別」，關掉「自動化」的「輪到我時通知」。

**最小範例**（bash，取自官方文件）。這個範例用到 `jq`，電腦上沒有的話要先裝。

```bash
d="$HOME/Library/Application Support/FocusCat/automation"; mkdir -p "$d"
jq -n --arg now "$(date -u +%Y-%m-%dT%H:%M:%SZ)" \
  '{contract:1, source:"範例工作", updated_at:$now, stale_after_minutes:10,
    jobs:[{id:"1", title:"整理週報", state:"running", updated_at:$now,
           step:"整理資料", step_started_at:$now}]}' \
  > "$d/.sample.json.tmp" && mv -f "$d/.sample.json.tmp" "$d/sample.json"
```

寫狀態檔時記得：

- 先寫暫存檔，再改名換上去。這樣外掛不會讀到寫一半的檔。
- 一支腳本一個檔，檔名就是它的代號。
- 跑的時候定期重寫，只改 `updated_at` 也可以。這樣腳本掛掉時，FocusCat 才看得出來。
- 做完的工作會一直列著，直到腳本把它從 `jobs` 拿掉。
- 只要有一份狀態檔讀不懂，整區就顯示「讀不到」，並指出是哪個檔。

完整格式寫在外掛規格 `plugins.md` 的「自動化工作外掛」一節。裝好的 app 裡附一份：`FocusCat.app/Contents/Resources/docs/plugins.md`。使用手冊也有[自動化工作](https://mpragnarok.github.io/FocusCat/manual/#automation)一節。

## 目前還沒有的功能

- **Jira 卡標「輪到你」**：有人在留言標記你、你還沒回，或卡改派回給你，那張卡就標「輪到你」並排到最前面。這個功能已合進主線，但最新發行版 v0.6.18 還不含。同一批還有：Jira 自己一張卡，以及「打開卡」「開始做」兩顆按鈕。
- **把番茄時間寫回 Jira 工時**：目前沒有。目前的發行版對 Jira 只讀不寫。
- **跟 Jira 上的估時對照**：目前沒有。FocusCat 自己可以估「這件要幾個番茄」，做完後小結會列出估幾個、實際幾個。

## 遇到問題

用 FocusCat 自己的「回報問題…」：

- 面板上「現在」那張卡右上角的「⋯」，最下面一項。
- 或是設定視窗最底下那排按鈕。

視窗下方會先列出「會送出的報告」。看起來像個資的內容會自動遮掉，例如卡號、email、token、網址。遮掉的地方有黃底，送出前可以先檢查。
