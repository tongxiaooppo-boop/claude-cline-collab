# claude-cline-collab

一個檔案協定，讓 Claude Code（指揮／審核）跟 Cline（寫程式）在同一個專案分工，
不用人在兩個聊天視窗之間複製貼上內容。

Cline 跟 Claude 之間沒有直接的 API/訊息通道，唯一共用的東西是檔案系統與 git。
這套協定把「人要幫兩邊搬運內容」降到「人只需要跟兩邊各說一聲開始/繼續」，
內容本身不再需要人工轉述。

## 怎麼用

1. **裝全域規則**：把 [`claude-cline-collab.md`](./claude-cline-collab.md) 複製到
   Cline 的全域規則資料夾：
   - Windows：`%USERPROFILE%\Documents\Cline\Rules\`
   - macOS/Linux：`~/Documents/Cline/Rules/`

   這樣裝一次，之後**每個專案**都自動套用，不用每個專案各放一份
   （Cline 只會讀工作區根目錄的 `.clinerules`，放在上層資料夾不會生效，
   所以「全部專案通用」要靠這個全域資料夾，不是專案內的檔案）。

2. **每個要用這套分工的專案**：從 [`collab-template/`](./collab-template) 複製
   出 `collab/to-cline.md`、`collab/from-cline.md` 兩個檔案到專案根目錄。
   規則裡寫死了：Cline 會先檢查專案根目錄有沒有 `collab/to-cline.md`，
   有才生效，沒有就當作這個專案沒有用這套模式。

## 運作方式

- **`collab/to-cline.md`**：Claude 把任務寫進去（做什麼、為什麼、規格裡沒寫死
  的地方 Claude 建議的解讀），每輪覆蓋。
- **`collab/from-cline.md`**：Cline 做完把報告寫進去（改了什麼、跑過的驗證、
  需要決定的事、建議下一步），每輪覆蓋——歷史紀錄本來就在 `git log` 裡，
  不需要在這個檔案裡疊加。
- Claude 審核時用 `git log`/`git diff` 看 Cline 實際改了什麼，搭配
  `from-cline.md` 的報告一起判斷——報告是 Cline 說它做了什麼，
  diff 才是它真的做了什麼。

## 設計取捨

刻意做得很陽春：沒有 MCP server、沒有背景輪詢、沒有額外依賴，純靠純文字檔案
加 git。這是為了跟現有工作流無縫接上，不是因為不知道有更完整的做法——如果
需要更進階的多 AI 互審（Claude / Codex / Gemini 互相 review、CLI 直接串接），
可以參考：

- [owgit/ai-collab-bridge](https://github.com/owgit/ai-collab-bridge)
- [axtonliu/ai-pair](https://github.com/axtonliu/ai-pair)

這個 repo 單純是我自己在用、記錄下來方便重建與分享的版本，不是要取代那些
更完整的方案。
