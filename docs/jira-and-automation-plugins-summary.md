# FocusCat 附帶外掛一頁摘要

> 依最新發行版 v0.6.18 寫成。細節與注意事項見[完整版](jira-and-automation-plugins.md)。

## FocusCat 是什麼

Mac 選單列上的番茄鐘。點選單列上的小角色會打開面板，面板只回答一件事：現在做哪一件。

需要 macOS 13 以上。到發行頁下載：<https://github.com/mpragnarok/FocusCat/releases/latest>。第一次打開要按右鍵，選「打開」。

**外掛**是一支小程式。FocusCat 定時叫它，它交回一份清單，清單裡的事就出現在面板上。正式版附兩支外掛，都不會自動裝上，要用的人自己按「安裝」。

## 兩支外掛各做什麼

| 外掛 | 做什麼 |
|---|---|
| Jira 我的卡 | 把指派給你、還沒做完的 Jira 卡帶進面板的「建議加進來」。預設每 5 分鐘查一次。 |
| 自動化工作 | 腳本寫一份狀態檔（描述工作跑到哪裡的 JSON 檔），那件工作就出現在面板的「自動化」區。外掛只讀檔，不執行任何東西。 |

## Jira 我的卡：最短設定

1. 到 Atlassian 建一個 API token（給程式用的一串密碼）：`id.atlassian.com/manage-profile/security/api-tokens`。token 只顯示一次，先複製下來。
2. 在 `~/.zshenv` 加三行環境變數（寫在設定檔裡、給程式讀的值）。寫在 `~/.zshrc` 會讀不到。
   - `JIRA_URL`：你們 Jira 的網址，`https://` 開頭
   - `JIRA_USERNAME`：登入 Jira 的 email
   - `JIRA_API_TOKEN`：剛才的 token
3. 結束 FocusCat 再打開。它只在打開時讀一次。
4. 「設定 → 外掛」→「可以加的外掛」→「Jira 我的卡」→「安裝」。
5. 點進去，在「狀態」那一列按「試跑」。紅字消失就成功了。

沒另外設定的話，只收「In Progress」的卡。FocusCat 對 Jira 只讀不寫，不會改你的卡。

## 自動化工作：最短設定

1. 「設定 → 外掛」→「可以加的外掛」→「自動化工作」→「安裝」。
2. 自己建資料夾，外掛不會幫你建：`~/Library/Application Support/FocusCat/automation/`
3. 讓腳本在跑的時候寫 `<代號>.json` 進這個資料夾，並且每隔一陣子重寫一次。
4. 檔裡的 `state` 寫目前狀態：`running`、`queued`、`done`、`needs_you`、`failed`。

變成 `needs_you` 或 `failed` 時，FocusCat 會通知你。狀態檔太久沒更新，面板會標【可能卡住了】，也會通知。

狀態檔的完整格式與範例在完整版。

## 還沒有的功能

Jira 卡標「輪到你」（已合進主線，v0.6.18 還不含）、番茄時間寫回 Jira 工時、跟 Jira 估時對照。

## 遇到問題

面板上「現在」那張卡右上角的「⋯」→「回報問題…」。送出前會先列出報告，像個資的內容會自動遮掉。
