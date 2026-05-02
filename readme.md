# 象棋麻將 · 棋仔自摸 / Chinese Chess Mahjong

> 一款以中國象棋棋子為牌面的麻將變體遊戲。純前端實現，打開瀏覽器即可遊玩。  
> A麻将 variant using Chinese chess pieces as tiles. Pure frontend, no backend required.

🎮 **立即遊玩 / Play Now**：下載或 clone 本 repo，打開 `index.html`

---

## 🪙 帥幣聲明 / Coin Disclaimer（重要 / Important）

> **本遊戲中的「帥幣」純粹為遊戲中虛擬的籌碼，用於模擬象棋麻將的籌碼機制（台數結算、勝負張力）。**  
> **"Commander Coin" is purely an in-game virtual token used to simulate the scoring mechanism of mahjong (fan calculation, win/loss tension).**
>
> - ❌ **絕無任何實際貨幣價值 / No real monetary value**
> - ❌ **不可兌換、轉讓或提取為真實金錢 / Non-exchangeable, non-transferable, non-redeemable**
> - ❌ **不涉及任何形式的真錢賭博 / Not involved in any form of real-money gambling**
>
> 請安心享受棋牌策略。 / Enjoy the strategic game with peace of mind.

---

## 🀄 遊戲簡介 / Game Introduction

象棋麻將（又稱棋仔自摸）使用象棋棋子代替麻將牌，共有三種版本：  
Chinese Chess Mahjong uses Chinese chess pieces instead of standard mahjong tiles. Three versions available:

| 版本 / Version | 人數 / Players | 總牌數 / Tiles | 胡牌條件 / Win Condition |
|----------------|----------------|----------------|---------------------------|
| 32張版 / 32-tile | 2人 / 2 | 32張 | 1組面子 + 1對子 / 1 set + 1 pair |
| 56張版 / 56-tile | 4人 / 4 | 56張 | 2組面子 + 1對子 / 2 sets + 1 pair |
| 64張版 / 64-tile | 4人 / 4 | 64張 | 2組面子 + 1對子 / 2 sets + 1 pair |

**牌的組合 / Tile Combinations：**
- **順子 / Sequence**：帥仕相、俥傌炮（紅方）；將士象、車馬包（黑方）
- **刻子 / Triplet**：任意三張相同的牌（兵兵兵、卒卒卒）
- **槓子 / Kong**：任意四張相同的牌
- **對子 / Pair**：任意兩張相同的牌（帥＋將可互為對子 / Shuai + Jiang can pair across colors）

**遊戲流程 / Game Flow：**
1. 決定莊家 → 發牌 → 莊家先打一張 / Dealer determined → Deal → Dealer discards first
2. 輪流進行：搶胡 > 吃牌（僅上家）> 摸牌 → 打牌 / Turn order: Win > Pung/Chow (only from upper player) > Draw → Discard
3. 達成胡牌條件即可宣告獲勝 / Declare win once conditions met

---

## 🎯 三種遊戲模式 / Three Game Modes

| 模式 / Mode | 說明 / Description | 帥幣結算 / Coin Settlement |
|-------------|--------------------|----------------------------|
| 🎰 賭桌模式 / Gambling | 與 AI 對戰，台數 × 底注 / Play vs AI, fan × bet | ✅ 贏加輸扣 / Win gain, lose deduct |
| 🏆 練習模式 / Practice | 免費練習，AI 難度可選 / Free practice, AI difficulty selectable | ✅ 贏給獎勵，輸不扣錢 / Win get reward, lose no cost |
| 🔬 分析模式 / Analysis | 手動控制所有玩家動作（沙盒＋悔步）/ Manual control (sandbox + undo) | ❌ 不結算 / No settlement |

---

## 🤖 AI 策略：納許均衡與實用近似 / Nash Equilibrium & Practical Approximation

### 核心發現 / Core Discovery

透過 **200 萬次 MCCFR 迭代實驗** 與 **完整數學證明**，本研究確認：  
Through **2 million MCCFR iterations** and **complete mathematical proof**, this research confirms:

> **象棋麻將 32 張兩人版是一個「已解決博弈」。**  
> **The 2-player 32-tile version of Chinese Chess Mahjong is a "solved game".**

**理論納許均衡 / Theoretical Nash Equilibrium**：精確 EV 策略（k 階 outs） / Exact EV strategy (k-level outs)  
**實用最優策略 / Practical Optimal Strategy**：outs‑smart_discard（1 階 outs 近似） / 1-level outs approximation

### outs‑smart_discard 策略說明 / Strategy Explanation

每回合決策邏輯 / Decision logic per turn：
1. **向聽數優先 / Shanten first**：選擇使向聽數最小的牌打出 / Discard the tile that minimizes shanten
2. **進張數最大化 / Maximize outs**：向聽數相同時，選擇使剩餘手牌「有效進張數（outs）」最大的牌 / If tie, maximize outs (weighted by remaining tiles)

此策略在 **shanten ≤ 1 時與理論均衡嚴格等價**（7,460 個狀態完整枚舉驗證，反例 = 0）。  
This strategy is **strictly equivalent to the theoretical Nash equilibrium when shanten ≤ 1** (verified by exhaustive enumeration of 7,460 states, counterexample = 0).

高向聽數下的理論差距經實驗確認**無法被任何已知策略利用**（對手建模優勢僅 +0.5%，在統計雜訊範圍內）。  
The theoretical gap at higher shanten values has been experimentally confirmed **inexploitable by any known strategy** (opponent modeling advantage only +0.5%, within statistical noise).

### 策略強度對比 / Strategy Strength Comparison

| 策略 / Strategy | vs 純隨機勝率 / vs Random | 說明 / Description |
|----------------|---------------------------|--------------------|
| 純隨機 / Random | 50%（基準 / baseline） | 完全無策略 / No strategy |
| smart_discard | 67% | 僅考慮向聽數 / Shanten only |
| **outs‑smart_discard** | **89%** | **機率加權，目前最強 / Probability-weighted, current strongest** |
| 對手建模 / Opponent modeling | 89.5% | 效益 +0.5%，無實質意義 / Negligible gain |

### 結構性先手優勢 / Structural First-Mover Advantage

當雙方均採用 outs‑smart_discard 時 / When both play outs‑smart_discard：
- 莊家勝率 / Dealer win rate：**52.6%**
- 閒家勝率 / Opponent win rate：**47.4%**
- 差距 **5.2%** 為遊戲規則的結構性先手優勢，任何策略都無法消除  
  The **5.2% gap** is a structural first-mover advantage inherent to the game rules, ineliminable by any strategy

---

## 📚 詳細研究報告 / Detailed Research Reports

完整的賽局理論分析、MCCFR 實驗數據與數學證明，請參閱獨立研究倉庫：  
For complete game theory analysis, MCCFR experimental data, and mathematical proof, please see the independent research repository:

🔗 **[棋類博弈研究總倉庫 / Board Game Research Repository](https://github.com/dffge552/board-game-research)**

包含 / Includes：
- 象棋麻將 AI 研究報告 v14（完整版）/ Chinese Chess Mahjong AI Research Report v14 (Full Version)
- 象棋麻將博弈理論 · 數學證明附錄 / Chinese Chess Mahjong Game Theory · Mathematical Proof Appendix
- 積分棋先手必勝策略分析 / Jifen Qi First-Move Win Strategy Analysis
- 雙步棋強制和棋之賽局理論分析 / Shuang Bu Qi Forced Draw Game Theory Analysis

---

## 🕹️ 操作說明 / Controls

- **摸牌 / Draw**：點擊「摸牌 ▶」按鈕 / Click "Draw" button
- **出牌 / Discard**：先點選手牌中的牌（黃色高亮），再點「出牌」/ Click a tile in hand (yellow highlight), then click "Discard"
- **吃／碰／槓／胡 / Chow/Pung/Kong/Win**：對方打出牌後，對應按鈕會亮起 / Buttons light up when an opponent discards
- **分析模式 / Analysis Mode**：可編輯手牌／棄牌、悔步、載入 MA‑FEN 局面 / Edit hand/discard, undo, load MA‑FEN notation

---

## 📜 授權 / License

MIT License © 2026 dffge552

---

## 🙏 致謝 / Acknowledgments

本專案的 AI 策略奠基於 MCCFR 實驗與博弈理論分析，感謝所有開源博弈研究社群的啟發。  
This project's AI strategy is built upon MCCFR experiments and game theory analysis. Thanks to all open-source game research communities for inspiration.