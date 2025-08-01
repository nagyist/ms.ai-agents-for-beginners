<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "8164484c16b1ed3287ef9dae9fc437c1",
  "translation_date": "2025-07-23T08:19:55+00:00",
  "source_file": "10-ai-agents-production/README.md",
  "language_code": "hk"
}
-->
# AI Agents in Production: 可觀察性與評估

當 AI Agent 從實驗性原型轉向真實世界應用時，理解其行為、監控其性能以及系統性地評估其輸出變得至關重要。

## 學習目標

完成本課程後，您將能夠了解/掌握：
- Agent 可觀察性與評估的核心概念
- 提升 Agent 性能、成本效益及有效性的技術
- 系統性地評估 AI Agent 的方法與內容
- 如何在部署 AI Agent 至生產環境時控制成本
- 如何為使用 AutoGen 構建的 Agent 添加監控功能

目標是讓您具備將「黑箱」Agent 轉變為透明、可管理且可靠系統的知識。

_**注意：** 部署安全且值得信賴的 AI Agent 非常重要。請參考 [構建值得信賴的 AI Agent](./06-building-trustworthy-agents/README.md) 課程。_

## Trace 與 Span

可觀察性工具（如 [Langfuse](https://langfuse.com/) 或 [Azure AI Foundry](https://learn.microsoft.com/en-us/azure/ai-foundry/what-is-azure-ai-foundry)）通常將 Agent 的運行表示為 Trace 和 Span。

- **Trace** 表示從開始到結束的一個完整 Agent 任務（例如處理用戶查詢）。
- **Span** 是 Trace 中的單個步驟（例如調用語言模型或檢索數據）。

![Langfuse 中的 Trace 樹](https://langfuse.com/images/cookbook/example-autogen-evaluation/trace-tree.png)

如果缺乏可觀察性，AI Agent 就像一個「黑箱」——其內部狀態和推理過程不透明，難以診斷問題或優化性能。有了可觀察性，Agent 就變成了「玻璃箱」，提供了透明性，這對於建立信任並確保其按預期運行至關重要。

## 為什麼可觀察性在生產環境中很重要

將 AI Agent 部署到生產環境會帶來一系列新的挑戰和需求。此時，可觀察性不再是「可有可無」，而是關鍵能力：

*   **調試與根本原因分析**：當 Agent 出現故障或產生意外輸出時，可觀察性工具提供的 Trace 能幫助定位錯誤來源。這對於涉及多次 LLM 調用、工具交互和條件邏輯的複雜 Agent 尤其重要。
*   **延遲與成本管理**：AI Agent 通常依賴於按 token 或調用次數計費的 LLM 和其他外部 API。可觀察性允許精確跟蹤這些調用，幫助識別過於緩慢或昂貴的操作。這使團隊能優化提示詞、選擇更高效的模型或重新設計工作流程，以管理運營成本並確保良好的用戶體驗。
*   **信任、安全與合規**：在許多應用中，確保 Agent 的行為安全且合乎道德非常重要。可觀察性提供了 Agent 行為和決策的審計記錄，可用於檢測和緩解問題，例如提示注入、生成有害內容或處理個人身份信息（PII）不當。例如，您可以通過查看 Trace 來了解 Agent 為何提供某個回應或使用特定工具。
*   **持續改進循環**：可觀察性數據是迭代開發過程的基礎。通過監控 Agent 在真實世界中的表現，團隊可以識別改進空間，收集數據以微調模型，並驗證更改的影響。這創造了一個反饋循環，將來自在線評估的生產洞察用於離線實驗和改進，從而逐步提升 Agent 的性能。

## 關鍵指標追蹤

為了監控和理解 Agent 的行為，需要追蹤一系列指標和信號。雖然具體指標可能因 Agent 的用途而異，但有些是普遍重要的。

以下是可觀察性工具通常監控的一些常見指標：

**延遲（Latency）：** Agent 的回應速度如何？長時間等待會對用戶體驗產生負面影響。您應通過追蹤 Agent 運行來測量任務和單個步驟的延遲。例如，一個在所有模型調用中花費 20 秒的 Agent，可以通過使用更快的模型或並行運行模型調用來加速。

**成本（Costs）：** 每次 Agent 運行的費用是多少？AI Agent 依賴於按 token 計費的 LLM 調用或外部 API。頻繁的工具使用或多次提示可能會迅速增加成本。例如，如果一個 Agent 為了微小的質量提升調用了 LLM 五次，您需要評估這樣的成本是否值得，或者是否可以減少調用次數或使用更便宜的模型。實時監控還可以幫助識別意外的成本激增（例如，因錯誤導致的過多 API 循環）。

**請求錯誤（Request Errors）：** Agent 失敗的請求數量是多少？這可能包括 API 錯誤或工具調用失敗。為了使您的 Agent 在生產環境中更具魯棒性，您可以設置回退機制或重試邏輯。例如，如果 LLM 提供商 A 無法使用，則切換到 LLM 提供商 B 作為備用。

**用戶反饋（User Feedback）：** 實施直接的用戶評估提供了寶貴的見解。這可以包括明確的評分（👍/👎，⭐1-5 星）或文字評論。持續的負面反饋應引起警惕，因為這表明 Agent 未按預期工作。

**隱式用戶反饋（Implicit User Feedback）：** 用戶行為即使沒有明確的評分，也能提供間接反饋。例如，立即重新措辭問題、重複查詢或點擊重試按鈕。如果您發現用戶反復詢問相同問題，這表明 Agent 未按預期工作。

**準確性（Accuracy）：** Agent 生成正確或期望輸出的頻率如何？準確性的定義可能不同（例如，解決問題的正確性、信息檢索的準確性、用戶滿意度）。第一步是定義成功對於您的 Agent 來說是什麼樣的。您可以通過自動檢查、評估分數或任務完成標籤來追蹤準確性。例如，將 Trace 標記為「成功」或「失敗」。

**自動評估指標（Automated Evaluation Metrics）：** 您還可以設置自動評估。例如，您可以使用 LLM 為 Agent 的輸出打分，例如是否有幫助、準確或無害。還有一些開源庫可以幫助您評估 Agent 的不同方面。例如，[RAGAS](https://docs.ragas.io/) 用於 RAG Agent，[LLM Guard](https://llm-guard.com/) 用於檢測有害語言或提示注入。

實際上，這些指標的組合能提供 AI Agent 健康狀況的最佳覆蓋。在本章的 [示例筆記本](../../../10-ai-agents-production/code_samples/10_autogen_evaluation.ipynb) 中，我們將展示這些指標在實際案例中的樣子，但首先，我們將學習典型的評估工作流程。

## 為 Agent 添加監控功能

為了收集 Trace 數據，您需要為代碼添加監控功能。目標是讓 Agent 代碼能夠生成 Trace 和指標，這些數據可以被可觀察性平台捕獲、處理和可視化。

**OpenTelemetry (OTel)：** [OpenTelemetry](https://opentelemetry.io/) 已成為 LLM 可觀察性的行業標準。它提供了一套 API、SDK 和工具，用於生成、收集和導出遙測數據。

有許多封裝現有 Agent 框架的監控庫，可以輕鬆地將 OpenTelemetry 的 Span 導出到可觀察性工具。以下是使用 [OpenLit 監控庫](https://github.com/openlit/openlit) 為 AutoGen Agent 添加監控的示例：

```python
import openlit

openlit.init(tracer = langfuse._otel_tracer, disable_batch = True)
```

本章的 [示例筆記本](../../../10-ai-agents-production/code_samples/10_autogen_evaluation.ipynb) 將演示如何為 AutoGen Agent 添加監控功能。

**手動創建 Span：** 雖然監控庫提供了良好的基礎，但在某些情況下可能需要更詳細或自定義的信息。您可以手動創建 Span 來添加自定義應用邏輯。更重要的是，您可以為自動或手動創建的 Span 添加自定義屬性（也稱為標籤或元數據）。這些屬性可以包括業務特定數據、中間計算結果或任何可能對調試或分析有用的上下文，例如 `user_id`、`session_id` 或 `model_version`。

以下是使用 [Langfuse Python SDK](https://langfuse.com/docs/sdk/python/sdk-v3) 手動創建 Trace 和 Span 的示例：

```python
from langfuse import get_client
 
langfuse = get_client()
 
span = langfuse.start_span(name="my-span")
 
span.end()
```

## Agent 評估

可觀察性為我們提供了指標，而評估則是分析這些數據（並執行測試）以確定 AI Agent 的表現如何以及如何改進的過程。換句話說，一旦您擁有了 Trace 和指標，如何利用它們來評估 Agent 並做出決策？

定期評估很重要，因為 AI Agent 通常是非確定性的，並且可能會隨著更新或模型行為的漂移而演變——如果沒有評估，您無法知道您的「智能 Agent」是否真的在做好工作，或者是否出現了退步。

AI Agent 的評估分為兩類：**離線評估**和**在線評估**。兩者都很有價值，並且相輔相成。我們通常從離線評估開始，因為這是部署任何 Agent 前的最低必要步驟。

### 離線評估

![Langfuse 中的數據集項目](https://langfuse.com/images/cookbook/example-autogen-evaluation/example-dataset.png)

這涉及在受控環境中評估 Agent，通常使用測試數據集，而不是實時用戶查詢。您使用經過精心挑選的數據集，這些數據集中您知道期望的輸出或正確行為，然後讓 Agent 在這些數據集上運行。

例如，如果您構建了一個數學文字題 Agent，您可能擁有一個 [測試數據集](https://huggingface.co/datasets/gsm8k)，其中包含 100 道題目及其已知答案。離線評估通常在開發過程中進行（並且可以成為 CI/CD 流水線的一部分），以檢查改進或防止退步。其優勢在於它是**可重複的，並且由於有標準答案，您可以獲得明確的準確性指標**。您還可以模擬用戶查詢，並將 Agent 的回應與理想答案進行比較，或者使用上述的自動指標。

離線評估的主要挑戰是確保您的測試數據集具有全面性並保持相關性——Agent 可能在固定的測試集上表現良好，但在生產中遇到完全不同的查詢。因此，您應該持續更新測試集，加入新的邊界情況和反映真實場景的示例。混合使用小型「冒煙測試」案例和大型評估集是有用的：小型集用於快速檢查，大型集用於更廣泛的性能指標。

### 在線評估

![可觀察性指標概覽](https://langfuse.com/images/cookbook/example-autogen-evaluation/dashboard.png)

這是指在實時、真實世界環境中評估 Agent，即在生產中實際使用時進行評估。在線評估涉及監控 Agent 在真實用戶交互中的表現並持續分析結果。

例如，您可能會跟蹤成功率、用戶滿意度分數或其他實時流量的指標。在線評估的優勢在於它**捕捉到實驗室環境中可能無法預見的情況**——您可以觀察模型隨時間的漂移（例如，隨著輸入模式的變化，Agent 的有效性是否下降），並捕捉測試數據中未包含的意外查詢或情況。它提供了 Agent 在真實環境中行為的真實畫面。

在線評估通常涉及收集隱式和顯式用戶反饋（如前所述），並可能運行影子測試或 A/B 測試（即新版本的 Agent 與舊版本並行運行以進行比較）。挑戰在於為實時交互獲取可靠的標籤或分數可能很困難——您可能需要依賴用戶反饋或下游指標（例如用戶是否點擊了結果）。

### 結合兩者

在線和離線評估並非互斥；它們高度互補。來自在線監控的洞察（例如，Agent 在某些新類型的用戶查詢中表現不佳）可以用於擴充和改進離線測試數據集。反之，在離線測試中表現良好的 Agent 可以更有信心地部署並在線監控。

事實上，許多團隊採用一個循環：

_離線評估 -> 部署 -> 在線監控 -> 收集新的失敗案例 -> 添加到離線數據集 -> 優化 Agent -> 重複_。

## 常見問題

在將 AI Agent 部署到生產環境時，您可能會遇到各種挑戰。以下是一些常見問題及其潛在解決方案：

| **問題**    | **潛在解決方案**   |
| ------------- | ------------------ |
| AI Agent 無法一致地執行任務 | - 優化提供給 AI Agent 的提示詞；明確目標。<br>- 確定是否可以將任務分解為子任務，並由多個 Agent 處理。 |
| AI Agent 陷入連續循環 | - 確保設置了明確的終止條件，讓 Agent 知道何時停止過程。<br>- 對於需要推理和規劃的複雜任務，使用專門針對推理任務的大型模型。 |
| AI Agent 的工具調用表現不佳 | - 在 Agent 系統外測試並驗證工具的輸出。 |

- 精煉已定義的參數、提示及工具命名。  |
| 多代理系統表現不一致 | - 精煉每個代理的提示，確保它們具體且彼此區分明確。<br>- 建立一個層次化系統，使用「路由」或控制代理來判斷哪個代理是正確的選擇。 |

許多這些問題可以通過設置可觀測性更有效地識別。我們之前討論的追蹤和指標有助於精確定位代理工作流程中問題發生的位置，使得除錯和優化更加高效。

## 成本管理

以下是一些管理部署 AI 代理至生產環境成本的策略：

**使用較小的模型：** 小型語言模型 (SLMs) 在某些代理使用案例中表現良好，並能顯著降低成本。如前所述，建立一個評估系統來判斷並比較其性能與較大型模型的表現，是了解 SLM 在您的使用案例中表現如何的最佳方式。考慮使用 SLM 來處理較簡單的任務，例如意圖分類或參數提取，而將較大型模型保留給需要複雜推理的任務。

**使用路由模型：** 一個類似的策略是使用多樣化的模型和大小。您可以使用 LLM/SLM 或無伺服器函數根據複雜性將請求路由至最適合的模型。這不僅能降低成本，還能確保在適合的任務上保持性能。例如，將簡單的查詢路由至較小、較快的模型，僅在需要複雜推理的任務上使用昂貴的大型模型。

**緩存回應：** 識別常見請求和任務，並在它們進入您的代理系統之前提供回應，是減少類似請求量的好方法。您甚至可以實施一個流程，使用更基本的 AI 模型來識別請求與緩存請求的相似程度。這種策略可以顯著降低常見問題或常規工作流程的成本。

## 實際應用

在[本節的示例筆記本](../../../10-ai-agents-production/code_samples/10_autogen_evaluation.ipynb)中，我們將看到如何使用可觀測性工具來監控和評估代理的示例。

## 上一課

[元認知設計模式](../09-metacognition/README.md)

## 下一課

[MCP](../11-mcp/README.md)

**免責聲明**：  
本文件已使用 AI 翻譯服務 [Co-op Translator](https://github.com/Azure/co-op-translator) 進行翻譯。雖然我們致力於提供準確的翻譯，但請注意，自動翻譯可能包含錯誤或不準確之處。原始語言的文件應被視為具權威性的來源。對於重要資訊，建議使用專業的人工翻譯。我們對因使用此翻譯而引起的任何誤解或錯誤解釋概不負責。