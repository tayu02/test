# ★★ 次の担当へ（OPUS3・最新総括）★★

> **これが最新の引き継ぎです。OPUS2（`digimon-simulator-edb-handoff-OPUS2.md`）は数値が古い（確定160と記載）ので破棄してよい。**
> **最初にやること**：本書 §3 のコマンドを実行し、実状態を**自分の目で**確認してから作業する。本書の数値も鵜呑みにしない（ドリフト防止）。
> 実証で確認した現在地（2026-06-19）＝**確定 242枚 / ドラフト 19枚 / validate 0エラー / 回帰 test_edb 85-0 / 機能 …+test_b2_batch25 7-0（ランナー集計 282/1※1は既知EX12-073）**。※第16弾で**エンジンに `selfSource` 条件を追加**（B1・§4e）＝HTMLも更新済み。。

---

## 0. このセッション（OPUS2→OPUS3）でやったこと（要約）

1. **実状態を実証**：引き継ぎOPUS2は「確定160」だが、実ファイルは**確定171**だった（LM-027〜032・EX12-073・BT9-095・ST15-10・EX8-049・P-080 等がOPUS2以降に追加され、メモは未更新だった）。さらに画像系・costReduction等の機能もOPUS2に未記載のまま実装済みだった。→ **引き継ぎ書は常に古い前提で、毎回 §3 で実測すること**。
2. **新しい目線の監査**：直近追加エントリを原文照合 → 主効果は忠実。ただし**既存エンジンのバグを2件発見**（§6）。
3. **B2 第12弾を確定（171→176）**：「相手1体の進化元をN枚破棄（`trash_source`）」の**無条件5枚**（§4）。`trash_source`はEDB初使用のため専用機能テストを新規作成し **20-0** 実証。
3b. **B2 第13弾を確定（176→179）**：**完全被覆できる残り3枚**＝P-079（条件付destroy dpMax）・P-082（条件付rest）・P-131（rest＋進化元static『自分のターンDP+2000』）。条件の両分岐＋静的継承を **test_b2_batch13 10-0** で実証（§4b）。**この過程で『無条件クリーン札はほぼ枯渇』を実証**＝以降の加速にはB1op追加が必要（§10）。
3n. **【B1家族・キーワード付与(静的全体)】第25弾＝`hasKeyword`のtargetをselector対応に拡張（241→242）**：「(条件)自分のデジモン全ては《X》を得る」が可能に(既存EDB未使用→回帰85/0維持)。BT6-082(ハックモン名orロイヤルナイツ特徴がいる間、シスタモン名全員ブロッカー＋登場時1ドロー)。OR条件・target selector・全体付与を実証。HTMLも更新。**test_b2_batch25 7-0**（§4n）。
3m. **【B1家族・キーワード付与(誘発)】第24弾＝`grant_keyword`にpicker追加（238→241）**：選択対象(N体)への一時付与を可能化(既存EDB未使用→回帰85/0維持)。BT4-058(このターン1体貫通)・ST5-09(次相手ターン終了まで1体ブロッカー)・ST5-12(同2体まで再起動)。tempKeywordsは次の自分アクティブフェイズで消去＝自分ターン発動で両持続を機能的充足。HTMLも更新。**test_b2_batch24 6-0**（§4m）。
3l. **【B1家族・キーワード付与】第23弾（235→238・改修ゼロ）**：既存の`static`ブロック＋`grant{op:'keyword'}`機構（`edbActiveGrants`→`hasKeyword`→実ゲーム処理が参照）で「《X》を得る」を実現。ST9-07/ST10-07(色デジモンいる間ブロッカー)・BT2-026(青テイマーいる間ジャミング)。ブロッカー判定9928/10066・貫通10084・ジャミング10166が`hasKeyword`参照を確認。**辞書/テキスト一致には戻らずEDB構造化のまま**。**test_b2_batch23 8-0**（§4l）。
3k. **【B1家族】第22弾＝`attackTarget`条件をエンジン追加（232→235）**：「プレイヤーにアタックしたとき」を判定する`attackTarget`を追加（executeAttackが対象記録後にアタック時効果を発火＝`inst._attackTarget`参照可・selfSource同型で**既存不干渉＝回帰85/0維持**）。BT2-012(自身DP+4000)/BT2-015(1ドロー)/BT2-019(memory+1)。**HTML/validatorも更新＝今回HTMLも納品**。**test_b2_batch22 6-0**（§4k）。
3j. **【B方針】第21弾＝既存opの横展開（226→232・改修ゼロ）**：salvage ST6-10(move trash→hand・upTo任意)、コスト払いBT2-077(destroy_own other→Lv5以下消滅)、dig「N枚まで」BT6-009/BT5-034(count:N,upTo:true)、dig「全て」BT1-048/EX2-030(count=公開数,upTo:false=全取得)。**test_b2_batch21 9-0**（§4j）。
3i. **【B方針】第20弾＝dig継続（218→226・改修ゼロ）**：`select_from_revealed`が`_edbRevealed`を消費する事を確認し**2連select『AとB1枚ずつ』**を解禁（BT5-007/BT5-020/BT8-058/BT10-046/BT4-009/BT4-023/BT2-044）＋コスト付dig BT14-042(rest_self→緑1枚)。**test_b2_batch20 8-0**（§4i）。
3h. **【B方針開始】第19弾＝コスト払い「することで」家族を解禁（212→218）**：エンジンに既存の`block.cost`機構を発見（`edbRunBlock`がコスト可否→任意確認→未払い不発を処理・既存ST14-11等で実績）＝**改修ゼロ**で解禁。BT4-037/BT6-041(セキュリティ破棄→相手DP減)・BT6-072(手札破棄→Lv4以下消滅)・BT4-078(オプション破棄→memory+1)・BT9-058/EX1-045(特定札破棄→2ドロー)。コスト3経路（支払う/支払わない/支払えない）を**test_b2_batch19 11-0**で検証（§4h）。
3g. **B2 第18弾を確定（202→212）**：現op希少3パターン10枚＝①**dig系**（reveal_deck_top→select_from_revealed→dispose_revealed）ST4-03/BT12-045/ST15-04/BT3-047/EX3-003、②条件付相手DP減 BT1-054(memory≥3)/BT3-042/BT4-041/BT4-044(sec≤3)、③BT1-061(相手2体DP-3000)。**dig系は今後さらに多数解禁可**。validatorにfeatureContains追加。**test_b2_batch18 10-0**（§4g）。
3f. **B2 第17弾を確定（192→202）**：現op完結の希少3パターン10枚＝①loot(draw→discard_hand) BT4-079/ST16-02/BT4-083/BT7-069、②相手DP-N/このターン BT1-062/BT1-055/BT5-042/BT5-039/ST3-11、③BT7-052(自身DP+5000＋消滅時memory+2)。新規活用=discard_hand/dp_mod負値・相手selector。**test_b2_batch17 9-0**（§4f）。
3e. **B2 第16弾＝【B1着手】`selfSource`条件をエンジンに追加（186→192の最後の3枚）**：お客様承認のもと、`edbCheckCondition`に「自分の進化元(stack)に名称/特徴一致カードがあるか」を判定する`selfSource`を追加（外科的・**既存挙動不変＝回帰85/0維持**）。これで**『進化元に〜がある』系 約120枚**が解禁可能に。実証としてBT5-010/BT5-015/BT5-024（グレイモン/ガルルモン系：selfSource主効果＋静的名称DP継承）を登録。**test_b2_batch16 10-0**（§4e）。**HTML/validatorも更新＝今回はHTMLも納品**。
3d. **B2 第15弾を確定（186→189）**：BT2-017(条件付destroy＋進化元static[自分ターン∧相手トラッシュ5枚以上]DP+1000)・EX2-020(2条件AND→recovery)・BT3-041(条件付 トラッシュ→セキュリティ move)。**test_b2_batch15 11-0**（§4d）。静的継承の複合zoneCount条件/exists nameContains/move(trash→security_top)を実証。
3c. **B2 第14弾を確定（179→186）**：**現op完結の希少・多様な5パターン8枚**＝activate(対象選択)・activate+lvMaxフィルタ・dp_mod(このターン/自分1体・他の自分1体)・bounce(レスト相手→デッキ下/進化元自動破棄)・**zoneCountによる条件分岐(セキュリティ枚数でdraw/memory)**。**test_b2_batch14 15-0**（§4c）。条件・once_per_turn・複数action逐次・同時誘発がEDBで表現可能と再確認（§10-4）。
4. **重大な既存不具合を発見・特定（§5）**：**EX12-073 が孤児参照**（カードデータがcards.jsonにもcard-overrides.jsonにも無いのにEDB/テストが参照）→ `getCard()` が null → `confirmPlayOption` でクラッシュ。OPUS2が「test_ex12073 7-0 / test_security_flip 6-0」と書いているのは**現ファイルでは虚偽**（実測：ex12073クラッシュ・security_flip 5-1）。**私のバッチとは無関係＝元171枚版でも同一に再現**。
5. お客様の方針を明確化（§2）：「**枚数が少ない**」＝バッチを小さくの意ではなく、**類似効果（同じパターンを共有するカード）が少ない＝レア/多様な効果パターンを優先**の意。長尾の独立効果から潰す。→ 第12弾の`trash_source`はこれに合致（EDB未登場の独立パターン・無条件の小集団）。

---

## 1. シミュレーターを動かすのに必要なファイル（4つ・名前そのまま）

1. `digimon-simulator-v5-version-Opus.html` … 本体（盤面・ルール・効果発動エンジン・カード画像表示）
2. `cards.json` … 全カード基礎データ（6,998枚）。**お客様の元データ＝無改変**
3. `effects-db-version-Opus.json` … **確定した自動発動データ（現在242枚）**。無いと従来パーサにフォールバック
4. `card-overrides.json` … カード修正/追加の差分（**現在5件**：BT20-072 / BT22-064 / BT22-073 / P-209 / ST22-06）。**お客様の元データ**

- ※**Webサーバー経由で開く**こと（GitHub Pages等）。`file://`直開きは `cards.json` 読込に失敗。
- ※カード画像（約6,900）は **GitHub raw** から読み込む。HTML冒頭 `CARD_IMG_BASE`（1615行付近）。
  - **★正しい値（固定・必ずこれにする）**：`https://raw.githubusercontent.com/tayu02/test/main/digimon cards`
  - **⚠ 再発注意：ユーザー名は `tayu02`。過去に2回 `music` に化けて画像が全部404になっている**（`music/test`は誤り）。リポジトリは `tayu02/test`（公開・`main`ブランチ・`digimon cards`フォルダ＝中に`ST`/`BT1~5`/`EX1~7`/`P`等のサブフォルダ）。**セッション開始時に §3 で必ず実値を確認すること**。
  - ⚠ プレースホルダガード `_cardImgReady()` は文字列 `USERNAME/REPO` しか弾かない。`music/test` のような“もっともらしい誤値”は通ってしまい、`true`のまま誤URLを叩く→全画像404→`onerror`で消える（テキスト表示になる）＝「画像が消えた」の症状。値そのものを目視確認するしかない。
  - [Opus追加] 読込失敗時の**二段フォールバック**：`img`の`onerror`で①`cards.json`の`image_url`（`digimon-cg-guide.com`）へ1回切替→②それも失敗ならテキスト表示。`convertRawCard`で`out.imageFallback`を保持し`convertDBCard`でも引き継ぐ（1691/1809行）。これで個別画像の欠けや一時的不通でも崩れにくい（ただし**本筋の修正はURLを正す**こと）。

## 1b. 実行には不要（開発・引き継ぎ用）

- `effects-db-drafts-version-Opus.json`（未検証ドラフト19枚。エンジンは `_verified:false/_draft:true` を実行しない＝本番未使用）
- 検査/テスト：`validate_effects_db-version-Opus.js`（DSL検査）/ `test_edb-version-Opus.js`（中核回帰85）/ **機能別テストは「1弾＝1ファイル」を維持**（`test_b2_batch13〜16`, `test_b2_trashsource`, `test_b2_w11`, `test_bt9095`, `test_card_images`, `test_turnend_bug`, `test_security_flip`, `test_ex12073` ほか）。
- **テスト運用（お客様と合意・2026-06-19）＝「個別ファイル＋自動ランナー」**：新しい弾を作るたびに**小さなテストを1本だけ追加**する（既存は触らない＝出力が軽い・落ちた時にファイル名で原因箇所が即分かる）。全実行は **`node run_all_tests-version-Opus.js`**（カレントの`test_*-version-Opus.js`を自動検出して集計・**落ちたファイルを名指し**・ランナー自体は弾を足しても変更不要）。
  - ⚠ **テストを1つの巨大ファイルに統合しないこと**（後任が良かれと統合しがちだが、編集の出力が重くなり・巨大ファイルのstr_replaceは事故りやすい〔本セッションで実証〕・コンテキストも食う）。統合版`test_b2_all`は不要（ランナーは二重計上回避のため除外）。
  - 現状ランナー集計＝**208 passed / 1 failed**。失敗1＝`test_security_flip`＋`test_ex12073`の**EX12-073孤児クラッシュ（§5・私のバッチと無関係の既存不具合）**で、ランナーが毎回これを可視化してくれる。
- 変換器：`p2_convert-version-Opus.js`（ドラフト生成・base-idガード付き）
- 監査記録：`draft-audit-version-Opus.md`
- 本書 `digimon-simulator-edb-handoff-OPUS3.md`

---

## 2. 採用方針（重要・お客様指示）

- **ハイブリッド**：パーサが信頼できる所＝軽量検証して確定。壊す所＝原文から手作業。
- **B2 を継続**（手作業verifiedを少数ずつ）。大量登録になるB1（エンジンop拡張で数百枚）は**出力負担を避けて後回し**。
- **優先順＝「類似効果が少ない（＝レア/多様な）パターン」から**（2026-06-19にお客様が明確化）。
  - 共有枚数の多い頻出効果（例：静的キーワード「ジャミング」「ブロッカー」、特殊進化テンプレ）はバルク/後回し。
  - **未カバー effectあり 5,891枚 / 異なる正規化パターン 3,303種、うち「1枚だけ」のパターンが 1,733種**（=巨大な長尾）。この長尾の独立効果を、現op群で**忠実かつ完全**に書けるものから拾う。
  - ※この集計は雑な正規化（数値→N・「名称」→X・先頭トリガ圧縮）に基づく**目安**。パーサは条件を黙って落とすので、最終判断は必ず**原文1対1照合**で。
- **昇格3条件**：①忠実（原文どおり）②完全（落ちる節が無い）③特殊進化/コスト等の別機構を取りこぼさない。1つでも欠ければ却下→保留。

---

## 3. 状態の検証方法（実行して実状態を確認する）★最重要

作業ディレクトリに HTML / effects-db-version-Opus.json / effects-db-drafts-version-Opus.json / cards.json / card-overrides.json / 各js を置いて:

```bash
node validate_effects_db-version-Opus.js                                    # 期待:「242カード / op35種 / エラーなし」
node validate_effects_db-version-Opus.js effects-db-drafts-version-Opus.json # 期待:「19カード / エラーなし」
node test_edb-version-Opus.js                                               # 期待:「85 passed / 0 failed」
node test_b2_trashsource-version-Opus.js                                    # 期待:「20 passed / 0 failed」(第12弾)
node test_b2_batch13-version-Opus.js                                        # 期待:「10 passed / 0 failed」(第13弾)
node test_b2_batch14-version-Opus.js                                        # 期待:「15 passed / 0 failed」(第14弾)
node test_b2_batch15-version-Opus.js                                        # 期待:「11 passed / 0 failed」(第15弾)
node test_b2_batch16-version-Opus.js                                        # 期待:「10 passed / 0 failed」(第16弾・selfSource)
node test_b2_batch17-version-Opus.js                                        # 期待:「9 passed / 0 failed」(第17弾)
node test_b2_batch18-version-Opus.js                                        # 期待:「10 passed / 0 failed」(第18弾・dig系)
node test_b2_batch19-version-Opus.js                                        # 期待:「11 passed / 0 failed」(第19弾・コスト払い)
node test_b2_batch20-version-Opus.js                                        # 期待:「8 passed / 0 failed」(第20弾・dig継続/2連select)
node test_b2_batch21-version-Opus.js                                        # 期待:「9 passed / 0 failed」(第21弾・salvage/destroy_ownコスト/dig全て)
node test_b2_batch22-version-Opus.js                                        # 期待:「6 passed / 0 failed」(第22弾・attackTarget条件)
node test_b2_batch23-version-Opus.js                                        # 期待:「8 passed / 0 failed」(第23弾・静的キーワード付与)
node test_b2_batch24-version-Opus.js                                        # 期待:「6 passed / 0 failed」(第24弾・grant_keyword picker)
node test_b2_batch25-version-Opus.js                                        # 期待:「7 passed / 0 failed」(第25弾・静的全体付与)
```

**★画像URLの確認（毎回・過去2回ここで事故＝必須チェック）**：
```bash
grep CARD_IMG_BASE digimon-simulator-v5-version-Opus.html
# 期待: https://raw.githubusercontent.com/tayu02/test/main/digimon cards
# ★ユーザー名が tayu02 であること。music 等になっていたら即修正（§1b参照）。全画像404の原因。
```

⚠ **既知の失敗（§5参照・要対応）**：
```bash
node test_ex12073-version-Opus.js        # 現状クラッシュ（getCard('EX12-073')=null）
node test_security_flip-version-Opus.js  # 現状 5 passed / 1 failed（EX12-073が場に置けない）
```
これらは **EX12-073のカードデータ欠落が原因**で、**私のバッチとは無関係**（元171枚版でも同一に再現）。OPUS2の「7-0/6-0」は現ファイルでは成立しない。

⚠ ツールのパスドリフト（未修正）：`p2_manifest.js` / `p2_coverage.js` は**無印**ファイルで、内部が**無印 effects-db.json（旧Fable5）**を読む。マニフェスト内訳は version-Opus の実体（242/19）を反映しない。使うなら参照先を直すこと。

---

## 4. このセッションの確定追加：B2 第12弾（5枚・確定171→176）

「**(進化時/登場時) 相手のデジモン1体の進化元をN枚破棄**」＝`trash_source`（**EDB初使用**）。全て**進化元(evolved_effect)・セキュリティ無し＝主効果のみで全ブロック被覆**。

|id|名称|原文|写像|
|--|--|--|--|
|ST2-09|ズドモン|進化時 相手1体の進化元を下から2枚破棄|on_evolve / `trash_source from:bottom count:2`|
|BT1-043|サーベルレオモン|進化時 相手1体の進化元を下から4枚破棄|on_evolve / from:bottom count:4|
|BT8-022|ユキアグモン|登場時 相手1体の進化元を上から1枚破棄|on_play / from:top count:1|
|EX1-012|ゴマモン|登場時 相手1体の進化元を下から1枚破棄|on_play / from:bottom count:1|
|P-003|ガブモン|登場時 相手1体の進化元を下から1枚破棄|on_play / from:bottom count:1|

### `trash_source` op の実証済み仕様（17924行）
- `p.from`：`'top'`=`stack.pop()`（**上**）／既定`'bottom'`=`stack.shift()`（**下**）。
- **配列の向き**＝`index0=下(最古)・末尾=上(最新)`（HTML 3210「末尾がスタックの上」、進化は旧カードを末尾pushで最上段の進化元に）。→ 写像は忠実。
- `p.count`＝破棄する進化元の枚数（**selectorのcountではない**）。
- 対象＝既定 相手1体。候補は**進化元を持つ(stack>0)もののみ**にフィルタ。複数候補は1体選択picker、0体は不発（`ctx.failed`）。
- **デジモン本体は消滅しない**（`cardId`不変）。退化(`devolve`)とは別物：devolveは上から下ろし、進化元が尽きると本体消滅。**「進化元破棄」は本体に触れない**＝今回`trash_source`を採用したのが正しい。
- parallel（`-P1`等）は **`edbFx` が `-P\d+$` を除去して base へ自動フォールバック**するので**明示登録は不要**（既存の明示`-P`エントリ33件は冗長だが無害）。

### 機能テスト（`test_b2_trashsource-version-Opus.js` 20-0）
発火・向き（下=shift×2／上=pop×1）・0体不発（モーダルも出ない）・破棄枚数>進化元のクランプ（クラッシュ無し）・複数候補picker（1体だけ失う）・parallelフォールバック を実証。

### 今回**見送った**同系候補（理由付き・次の担当への申し送り）
全ブロック被覆の原則で、**進化元(evolved_effect)に実効果を持つ**ため主効果だけEDB化すると継承効果が消える：
- **BT6-027**（進化元＝アタック時[ターン1回]条件付きアクティブ）, **BT13-065**（進化元＝消滅置換）, **BT13-081**（進化元＝相手ターン終了時ドロー＋破棄）, **EX6-045**（進化元＝アタック終了の置換）, **EX4-017**（進化元＝条件付きメモリー+1）, **P-131**（進化元＝自分のターンDP+2000＝静的・**これは表現可**だが本体rest＋継承の2機構になるため別途）。
- **EX3-019**（「進化元を**選んで**1枚破棄」）＝`trash_source`はstack内の任意選択を持たず（top/bottomのみ）→ 表現不能で保留。
- 「相手1体を**手札に戻す**。そのデジモンの進化元は破棄する」系（複合）は別パターン（168枚規模の大半がこれ）。`bounce`＋進化元処理の合わせ技で、次の独立バッチ候補。

---

## 4b. このセッションの確定追加：B2 第13弾（3枚・確定176→179）

「**完全被覆できる残り**」を集めた。`trash_source`の無条件札（第12弾）で**evolved/sec無しの単文クリーン札は枯渇**したため、ここからは**条件付き／継承効果を伴う札**が中心になる（§10）。

|id|名称|effect/evolved|写像|
|--|--|--|--|
|P-079|アグモン|登場時 **赤テイマーがいるとき** DP3000以下の相手1体消滅|on_play / `condition.exists(my,テイマー,colorAny[赤])` ＋ `destroy filter:{dpMax:3000}`|
|P-082|クネモン|登場時 **緑テイマーがいるとき** 相手1体レスト|on_play / exists(緑テイマー) ＋ `rest`|
|P-131|プテロモン|effect:登場時 相手1体レスト ／ evolved:自分のターン このデジモンDP+2000|`effects:[on_play rest]` ＋ `inherited:[static, condition:{isMyTurn:true}, grant:{dp_mod,self,+2000}]`|

### 実証メモ（第13弾）
- **条件 `exists`** は P-080 と同型で実証済み。`dpMax` は `edbSelectCandidates` が `getDP`（現在値）で判定（17344行）。
- **静的継承（恒常）**：`inherited:[{kind:'static', condition:{isMyTurn:true}, grant:{op:'dp_mod', target:'self', value:2000}}]`。ST14-06（特徴条件付き）と異なり**条件なしの恒常版**で、`{isMyTurn:true}` 単独でも動作することを確認（getDPが自分ターンのみ+2000、P-131を進化元に持つ本体だけに加算）。
- **機能テスト `test_b2_batch13-version-Opus.js` 10-0**：条件の**両分岐**（テイマー在=発火／不在=不発／色違い=不発／DP超過=対象外）と、静的継承（自分ターンのみ／進化元保持時のみ）を実証。**条件付きはDSL検査だけでなく必ず両分岐を機能テストする**（鉄則§7-6）。



## 4c. このセッションの確定追加：B2 第14弾（8枚・確定179→186）

お客様の方針を再確認：**op追加で数百枚がまとめて解禁される型はB1（後回し）。op追加では量産できない＝現op群だけで完結する希少・多様な型をB2として優先**。本弾はその趣旨で、現op群の**未活用op**を中心に5パターンを処理。

|id|名称|原文|写像|
|--|--|--|--|
|BT1-036|ガルルモン|登場時 自分のデジモン1体をアクティブ|on_play / `activate`（既定 my/battle/rested:true/1）|
|BT3-025|エクスブイモン|進化時 Lv.4以下の自分1体をアクティブ|on_evolve / `activate` filter:{lvMax:4}|
|ST1-08|ガルダモン|進化時 このターン 自分1体 DP+3000|on_evolve / `dp_mod` selector:{my,battle,1} value:3000|
|BT7-010|タスクモン|進化時 このターン 自分1体 DP+2000|on_evolve / `dp_mod` …value:2000|
|BT5-011|メラモン|進化時 このターン **他の**自分1体 DP+3000|on_evolve / `dp_mod` selector:{my,battle,**other:true**,1}|
|BT5-055|バンチョーリリモン|消滅時 レスト相手1体をデッキ下へ（進化元は破棄）|on_destroy / `bounce` selector:{opp,デジモン,**rested:true**,1} to:**deck_bottom**|
|P-028|パルスモン|登場時 セキュリティ3枚以上なら1ドロー／3枚以下ならメモリー+1|on_play × **2ブロック**：cond `zoneCount(my/security/>=3)`→draw／`zoneCount(<=3)`→memory|

### 実証メモ（第14弾）
- **`activate`（対象選択版・17956）**：既定で**レスト中の自分のみ**を候補化（アクティブ済みは対象外＝原文どおり）。`filter`でLv制限可。`activate_self`（自身専用）とは別物。
- **`dp_mod`（selector可・17836）**：`selector`省略=自身、`{side:'my',count:N}`で対象選択、`other:true`で自身除外。`tempDP`に加算＝**このターン終了までの一時加算**（原文『このターンの間』と一致。恒常DPには使えない）。
- **`bounce`＋進化元自動破棄**：`edbMoveInstTo`（17755）が移動時に`inst.stack`を相手トラッシュへ送る＝**「そのデジモンの進化元は破棄する」はbounce単独で被覆**（第2文は冗長）。`to:'deck_bottom'`は`deck.unshift`＝先頭=一番下。
- **条件分岐は同一トリガの複数ブロックで表現**（P-028）。**境界（3枚）は両条件成立→同時誘発**で、エンジンが順番選択モーダル（`openSimultaneousModal`→`selectSimultaneousOrder(idx)`、1回呼べば残りもキュー処理）を出し**両方発揮**＝デジモンTCGの同時誘発ルール通りで**忠実**。テストはこのモーダルを解決して両発揮を実証。
- **機能テスト `test_b2_batch14-version-Opus.js` 15-0**。

---

## 4d. このセッションの確定追加：B2 第15弾（3枚・確定186→189）

evolved/sec無しの**無条件**単一効果は枯渇したため、本弾は「条件付き」「進化元(static)併載」の**現op完結**の希少パターン。

|id|名称|原文|写像|
|--|--|--|--|
|BT2-017|メガログラウモン|effect:進化時 赤テイマー条件→DP3000以下消滅 ／ evolved:自分ターン 相手トラッシュ5枚以上の間 DP+1000|`effects:[on_evolve, cond exists(赤テイマー), destroy dpMax]` ＋ `inherited:[static, cond and[isMyTurn, zoneCount(opp/trash/>=5)], dp_mod self+1000]`|
|EX2-020|ロップモン|登場時 セキュリティ3枚以下∧「李小春」存在→リカバリー+1|on_play / cond `and[zoneCount(my/security/<=3), exists(my/テイマー/nameContains[李小春])]` → `recovery count:1`|
|BT3-041|ケルビモン|アタック時 セキュリティ3枚以下→自トラッシュの黄デジモンをセキュリティ上へ|on_attack / cond zoneCount(my/security/<=3) → `move {from:trash, type:デジモン, filter:colorAny[黄], to:security_top, count:1}`|

### 実証メモ（第15弾・`test_b2_batch15` 11-0）
- **静的継承に複合条件**：`inherited:[{kind:'static', condition:{and:[{isMyTurn},{zoneCount:{side:'opp',zone:'trash',op:'>=',value:5}}]}, grant:{dp_mod,self,+1000}}]`。T14（and[isMyTurn, self.featureAny]）と同じ静的評価で**zoneCountも条件に使える**ことを確認（相手トラッシュ枚数でDPが増減）。
- **`exists` の `nameContains`**：`exists:{side:'my',zone:'battle',type:'テイマー',filter:{nameContains:['李小春']}}`＝盤面に特定名のテイマーがいるか。2条件は`and`で結合。
- **`move` で trash→security**：`{op:'move', side:'my', from:'trash', type:'デジモン', filter:{colorAny:['黄']}, to:'security_top', count:1}`。moveは**常にpicker**を開く（1候補でも）→テストは`edbTogglePick/edbConfirmPick`で解決。`recovery`はデッキ上→セキュリティ上（17824）。

## 4e. このセッションの確定追加：B2 第16弾【B1着手：`selfSource`をエンジン追加】（3枚・確定189→192）

お客様承認のもと、**条件1個でライン札一族を解禁**する低リスクB1を実施。希少B2が枯れた局面の打開策（§10-7）。

### ★エンジン改修（HTML）：`selfSource` 条件を追加
- 場所：`edbCheckCondition`（HTML ~17276、`cond.self` の直後）。実装：
  ```js
  if (cond.selfSource) {  // 自分の進化元(stack)に名称/特徴一致カードがあるか
    const _st = (inst && inst.stack) || [];
    return _st.some(id => edbCardMatchesFilter(getCard(id), cond.selfSource));
  }
  ```
- `selfSource` の値は**通常のフィルタ**（`nameIs`完全一致 / `nameContains`部分一致 / `type` / `featureAny` / `featureContains` …）。`edbCardMatchesFilter`を再利用するので語彙は共通。
- **安全性**：既存entryは`selfSource`未使用＝**挙動完全不変**。**回帰85/0維持を実測**（HTML改変後に全テスト再走）。`validate_effects_db-version-Opus.js`の`KNOWN_COND_KEYS`にも`selfSource`を追加済み。
- ⚠ **edb_module.js は本環境に存在しない**ため、改修は**HTMLに直接**入れた（validatorの`MODULE_PATH`もHTMLにフォールバック）。**もし将来 edb_module.js から sync する運用に戻すなら、同じ1ブロックを edb_module.js 側にも入れること**（入れ忘れるとHTMLが上書きされ消える）。今回**HTMLを納品物に含める**。

### 登録3枚（実証）
|id|名称|原文の要点|写像|
|--|--|--|--|
|BT5-010|グレイモン|進化時 進化元に「アグモン」→memory+1 ／ 自分ターン 名称グレイモン/オメガモン含む間 DP+2000|`cond selfSource{nameIs:"アグモン"}`→memory ＋ `inherited static and[isMyTurn, self{nameContains}]`+2000|
|BT5-015|メタルグレイモン:アルタラウス|進化時 進化元に名称メタルグレイモン含むデジモン→DP4000以下消滅 ／ 自分ターン静的DP+2000|`cond selfSource{type:デジモン,nameContains:["メタルグレイモン"]}`→destroy ＋ 静的+2000|
|BT5-024|ガルルモン|進化時 進化元に「ガブモン」→memory+1 ／ **お互いのターン** 名称ガルルモン/オメガモン含む間 DP+1000|`cond selfSource{nameIs:"ガブモン"}`→memory ＋ `inherited static self{nameContains}`（isMyTurn無し＝両ターン）+1000|

### 実証メモ（`test_b2_batch16` 10-0）
- **「「X」(括弧のみ)＝`nameIs`完全一致」「「Xを含む」＝`nameContains`部分一致」を区別**（「アグモン」と「メタルグレイモン**を含む**」）。`nameIs`は`c.name===X || cardNameMatches()`で公式名称ルール対応。
- 進化元staticの`self`は**holderの名称**を見る（`edbStaticDpBonus`が継承staticにholderの`inst`を渡す・17458）。「自分のターン」は`and[isMyTurn,...]`、「お互いのターン」は`isMyTurn`を付けない（両ターン適用）。
- ※テスト作成の落とし穴：静的DPの基準は**素のカードDP**（`CARD_DB[id].dp`）で取ること。`makeG`は`isMyTurn:true`なので、その状態で`getDP`を基準に取ると既に加算済みになり誤判定する（今回1度踏んで修正）。
- **約120枚**が`selfSource`で解禁可能（多くは特殊進化/ターン1回等の別要素も持つので個別に被覆要確認。まずBT5系の素直なものから）。




## 4f. このセッションの確定追加：B2 第17弾（10枚・確定192→202）

selfSourceの完全被覆できる残り（BT5-010/015/024）を取り切った後、別の現op希少パターンを処理。`evolved/sec`無しの主効果のみ。

|パターン|id|要点|写像|
|--|--|--|--|
|① loot（draw→discard）|BT4-079・ST16-02|登場時 1ドロー→手札1破棄|on_play / `[draw1, discard_hand1]`|
||BT4-083|消滅時 2ドロー→1破棄|on_destroy / `[draw2, discard_hand1]`|
||BT7-069|消滅時 3ドロー→2破棄|on_destroy / `[draw3, discard_hand2]`|
|② 相手DP-N（このターン）|BT1-062/BT1-055/BT5-042|進化時/登場時 相手1体 DP-8000/-3000/-4000|`dp_mod selector:{opp,デジモン,1} value:-N`|
||BT5-039・ST3-11|消滅時/アタック時 相手1体 DP-3000/-4000|同上（トリガ違い）|
|③ 自身DP＋消滅時memory|BT7-052|進化時 自身DP+5000/このターン＋消滅時memory+2|on_evolve `dp_mod value:5000`（selector省略=自身）＋on_destroy `memory+2`|

### 実証メモ（`test_b2_batch17` 9-0）
- **`discard_hand`（18014）**：自分の手札から指定枚数を選び破棄（picker・**mandatory**）。「1ドロー。その後手札1枚破棄」は引いた直後で必ず実行可。`draw`→`discard_hand`を`actions`逐次で「その後」を表現。テストはpickerを`edbTogglePick/edbConfirmPick`で解決。
- **`dp_mod`は負値＆相手selector可**：`{op:'dp_mod', selector:{side:'opp',zone:'battle',type:'デジモン',count:1}, value:-8000}`＝tempDPに加算でこのターンのDP減（消滅ではない）。`selector`省略時は自身（BT7-052の+5000）。

## 4g. このセッションの確定追加：B2 第18弾（10枚・確定202→212）

`evolved/sec`無しの主効果のみ。**dig系（デッキ操作）**という大鉱脈に着手。

|パターン|id|要点|写像|
|--|--|--|--|
|① dig（オープン→一致を手札→残り処理）|ST4-03・BT12-045|登場時 上1枚→緑デジモンなら手札/残りデッキ下|`[reveal_deck_top{count:1}, select_from_revealed{to:hand, selector:{type:デジモン,filter:{colorAny:[緑]},count:1,upTo:false}}, dispose_revealed{to:deck_bottom}]`|
||ST15-04|上1枚→黒カードなら手札/残り破棄|select filter colorAny[黒]（型不問）, dispose to:trash|
||BT3-047|消滅時 上3枚→Lv4-5デジモン1枚を手札/残りデッキ下|reveal3, select filter {lvMin:4,lvMax:5}|
||EX3-003|アタック時 上3枚→特徴竜デジモン1枚を手札/残りデッキ下|select filter {featureContains:[竜]}|
|② 条件付 相手DP減|BT1-054|アタック時 memory≥3→相手1体DP-2000|cond `{memory:{op:'>=',value:3}}`＋dp_mod opp -2000|
||BT3-042/BT4-041/BT4-044|sec≤3→DP-6000/-4000/-3000|cond zoneCount(security≤3)＋dp_mod opp|
|③ 相手2体DP減|BT1-061|登場時 相手2体DP-3000|dp_mod selector count:2 value:-3000|

### 実証メモ（`test_b2_batch18` 10-0）
- **dig系3op**：`reveal_deck_top{count:N}`（デッキ上→`G._edbRevealed`）→`select_from_revealed{to, selector}`（`zone:'revealed'`から条件一致を選び`to`へ・**`selector.upTo:false`で「一致あれば取得必須/無ければ0」**＝「〜なら手札に加える」を忠実化・pickerは`edbTogglePick/edbConfirmPick`で解決）→`dispose_revealed{to}`（残りを`deck_bottom`/`trash`へ）。**今後ST4系・BT8/9系等の多数が解禁可**（ただし「2色のカード」等の複合フィルタは未対応＝個別判断）。
- selectorのフィルタは**zone非battle時**`type`直書き＋他は`filter:`配下（`edbSelectCandidates` 17343）。`featureContains`はvalidator許可リストに追記済。
- **`memory`条件**は`-G.memory`基準＝「自分メモリーがN以上」＝`{memory:{op:'>=',value:N}}`（17291）。「〜のとき」分岐の頻出パターン。
- dig系の「好きな順番でデッキ下」の順序選択は非対応（デッキ最下層行きで実質影響軽微）。

## 4h. このセッションの確定追加：B2 第19弾＝【B方針・コスト払い家族】（6枚・確定212→218）

**お客様方針B（家族の一括解禁）の第一弾**。最大級の家族「することで」（未カバー約346枚）に着手。**重要：エンジンは既に`block.cost`機構を持っており、改修ゼロで書ける**（慎重方針に最適）。

### ★コスト機構（既存・改修不要）
`edbRunBlock`（17579）はブロックに`cost:[...]`があると：`edbCanPayCosts`（支払可否判定）→ 払えなければ不発 → `edbOpenCostConfirm`（「支払って発動／発動しない」モーダル＝**任意**）→ コストop実行（`ctx.isCost=true`）→ `ctx.aborted`なら不発 → `actions`（効果）実行。「Xすることで、Yする」＝`cost:[Xのop]` + `actions:[Yのop]`。**既存ST14-11/ST5-14/BT25-082で実績**。
- 使えるコストop（`edbCanPayCosts`が判定）：`rest_self`（このデジモンをレスト）/`discard_hand`（手札破棄・`type`/`filter`で対象限定可）/`trash_security_self`（セキュリティ破棄）/`destroy_own`（自分デジモン消滅）/`move`。
- ※コストが「メモリー-N」「進化元破棄」「自分デジモン1体をレスト(=選択)」等は未対応op→その札は別途。

### 登録6枚
|id|原文の要点|cost|actions|
|--|--|--|--|
|BT4-037/BT6-041|セキュリティ1枚破棄→相手1体DP-2000/-5000|`trash_security_self{count:1}`|`dp_mod opp -N`|
|BT6-072|手札1枚破棄→Lv.4以下の相手1体消滅|`discard_hand{count:1}`|`destroy opp filter:{lvMax:4}`|
|BT4-078|手札のオプション1枚破棄→memory+1|`discard_hand{count:1,type:オプション}`|`memory+1`|
|BT9-058|手札の特徴X抗体1枚破棄→2ドロー|`discard_hand{count:1,filter:{featureAny:[X抗体]}}`|`draw2`|
|EX1-045|手札のマシーン型/サイボーグ型デジモン破棄→2ドロー|`discard_hand{count:1,type:デジモン,filter:{featureAny:[…]}}`|`draw2`|

### 実証メモ（`test_b2_batch19` 11-0）
- **コスト3経路を検証**：①支払う（`edbResolvePending(true)`→コスト控除→効果発揮）②支払わない（`edbResolvePending(false)`→何も起きない）③支払えない（`edbCanPayCosts`がfalse→モーダルも出ず不発）。
- discard_handコストの`type`/`filter`が払える札を正しく限定（X抗体/オプションが手札にある時だけ可）。テストではコスト確認を`edbResolvePending`、破棄pickerを`edbTogglePick/edbConfirmPick`で解決。
- **今後**：「することで」は他に多数。ただしメモリーコスト/特殊進化/付与/参照効果（破棄カードのDP流用等）を含むものは個別判断。素直なコスト→現op効果のものから継続可能。

## 4i. このセッションの確定追加：B2 第20弾＝【B方針・dig継続】（8枚・確定218→226）

改修ゼロ（既存op）。dig系の表現力を広げた。

### ★2連select『AとB1枚ずつ』が可能
`select_from_revealed`は選んだ札を`G._edbRevealed`から**spliceで消費**する（17343付近）。よって`select_from_revealed`を2回続けると、2回目は1回目の残りから選べる＝「その中のAとB1枚ずつを手札に加える」を**2連selectで表現**できる。各selectは`count:1, upTo:false`（一致あれば取得）。
- フィルタ語彙の確認：**`nameContains`はEDB側で配列**（`f.nameContains.some(...)`＝OR・17337）。**`featureContains`は単一文字列扱い**（17335）なので**1要素配列**で渡す（複数substringは不可）。「特徴Xを持つ」=`featureAny:[X]`(完全一致)、「特徴Xを含む」=`featureContains:[X]`(部分一致・1要素)。複合は1フィルタに併記（例：緑テイマー=`type:テイマー`＋`filter:{colorAny:[緑]}`）。

### 登録8枚
|id|要点|構造|
|--|--|--|
|BT14-042|レストすることで→上3→緑1枚|`cost:[rest_self]`＋`[reveal3, select{colorAny:[緑]}, dispose]`|
|BT5-007/BT5-020|上3→名称AとB(デジモン)1枚ずつ|reveal3, select{type:デジモン,nameContains:[A]}, select{…[B]}, dispose|
|BT8-058|上4→『グレイモン』含む＋『竜人型』持つ|select{nameContains:[グレイモン]}, select{featureAny:[竜人型]}|
|BT10-046|上4→『植物型』含む＋『妖精型』持つ|select{featureContains:[植物型]}, select{featureAny:[妖精型]}|
|BT4-009/BT4-023|上3→ハイブリッド体デジモン＋色テイマー|select{type:デジモン,featureAny:[ハイブリッド体]}, select{type:テイマー,colorAny:[色]}|
|BT2-044|上3→Lv5デジモン＋緑テイマー|select{type:デジモン,lvIs:5}, select{type:テイマー,colorAny:[緑]}|

### 実証メモ（`test_b2_batch20` 8-0）
- 2連selectで**AとB両方が手札に入る**ことを確認（残りは正しくデッキ下）。逐次pickerは`edbTogglePick(0)/edbConfirmPick`をループ解決。
- コスト付dig（BT14-042）：`rest_self`コスト→支払う(レスト＋dig発揮)／既レストで支払えない(不発)を確認。
- **残dig**：「2色のカード」等の複合色フィルタ(BT8-009系)、「全て」(count=全一致)、「N枚まで」(count:N,upTo:true)は別途。デジバースト/吸収進化等のキーワード保持フィルタも要検討。

## 4j. このセッションの確定追加：B2 第21弾＝【B方針・既存opの横展開】（6枚・確定226→232）

改修ゼロ。既存opの未使用機能を実証して別パターンを開けた。

|id|パターン|要点|構造|
|--|--|--|--|
|ST6-10|salvage（任意）|トラッシュ→手札（紫デジモン1枚）|`move{from:trash,to:hand,type:デジモン,filter:{colorAny:[紫]},count:1,upTo:true}`|
|BT2-077|コスト払い|他の自分1体消滅→Lv5以下の相手消滅|`cost:[destroy_own{other:true}]`＋`destroy opp filter:{lvMax:5}`|
|BT6-009|dig「N枚まで」|名称ハックモン系2枚まで|select `count:2,upTo:true`（nameContains配列OR）|
|BT5-034|dig「N枚まで」|戦士型/聖騎士型の黄2枚まで|select `filter:{featureAny:[…],colorAny:[黄]},count:2,upTo:true`|
|BT1-048/EX2-030|dig「全て」|黄/黒テイマー全て|select `count:公開数,upTo:false`＝一致を全取得|

### 実証メモ（`test_b2_batch21` 9-0）
- **`move`の任意化**：`upTo:true`でキャンセル経路が`next()`のみ（abortしない）＝「戻せる」。対象0でも安全（何もしない）。`from:trash`等を直書き（selectorではなくop直下に`from/to/side/type/filter/count/upTo`）。
- **`destroy_own`はcost可＆`other:true`**（17xxx：destroyにside:my,other:p.otherを渡す）。「他の自分のデジモン」を消滅コストにできる。支払えない（他がいない）時は不発。
- **dig「全て」**：`count`=公開枚数・`upTo:false`にすると、pickは`min(count,一致数)`を**必須選択**＝一致を全取得。テストで黄テイマー2枚を強制取得を確認。「N枚まで」は`upTo:true`。
- pick解決は`_edbPick.items.length`と`_edbPick.opt.count`を読み、`min`枚をtoggle→confirmする汎用ヘルパで対応。

## 4k. このセッションの確定追加：B1家族【プレイヤーにアタック】第22弾（3枚・確定232→235）

純EDB（無改修）の素直な鉱脈をほぼ掘り尽くしたため、selfSource同型の**最小・外科的なエンジン追加**を1つ実施。

### ★`attackTarget`条件をエンジン追加（17287付近）
```js
if (cond.attackTarget) {  // 'player' / 'digimon'
  const _tgt = (inst && inst._attackTarget) || G._attackTarget;
  return !!_tgt && _tgt.type === cond.attackTarget;
}
```
- 根拠：`executeAttack`（9823〜）は**①`attacker._attackTarget={type,instId}`を記録 → ④アタック時効果（EDB含む）を発火**の順。よってアタック時条件で`inst._attackTarget.type`を確実に参照できる（`turn_start`のような死に条件ではない）。
- 既存カードは未使用＝**既存挙動不変・回帰85/0維持**（カード追加前に実測）。validatorの`KNOWN_COND_KEYS`に`attackTarget`追記。
- ※`edb_module.js`は本環境に存在せず、変更はHTML（真実の源）に直接。将来モジュール同期を復活させるなら同じ条件をそちらにも。

### 登録3枚（いずれも on_attack＋`condition:{attackTarget:'player'}`）
|id|効果|actions|
|--|--|--|
|BT2-012|プレイヤー攻撃時 自身DP+4000(このターン)|`dp_mod value:4000`|
|BT2-015|プレイヤー攻撃時 1ドロー|`draw count:1`|
|BT2-019|プレイヤー攻撃時 メモリー+1|`memory value:1`|

### 実証メモ（`test_b2_batch22` 6-0）
- 各カードで**対象=プレイヤー→発動／対象=デジモン→不発**の両分岐を確認（テストは`me._attackTarget={type:'player'|'digimon'}`をセットしてからアタック時を発火）。
- 他の「プレイヤーにアタック」札（ST15-05/EX1-009/EX1-010等）は**ブロッカー/進撃/Sアタック＋等の印刷キーワードやテイマー条件**を伴う。印刷キーワードがeffectテキスト解析で検出される場合、EDB登録(排他ルーティング)で失われる懸念があるため**個別に要確認**（まずキーワードを伴わない素直な3枚のみ登録）。

## 4l. このセッションの確定追加：B1家族【キーワード付与・静的】第23弾（3枚・確定235→238）

お客様確認「効果と動作の繋ぎは辞書/テキスト一致に戻っていないか」への回答も兼ねる。

### ★アーキテクチャ確認（効果→動作の繋ぎ）
- **EDB（effects-db）は構造化エントリをカードIDで引いて決定的に実行**。テキスト一致辞書ではない。ブロックには2種：
  - `triggered`：`on_play/on_evolve/on_attack/on_destroy`等で`actions`(op列)を1回実行。
  - `static`：継続効果。常時жив kindで、`condition`が成立する間だけ`grant`が有効。**静的DP**は`edbStaticDpBonus`が`grant{op:'dp_mod'}`を加算、**静的キーワード**は`edbActiveGrants('keyword')`が`grant{op:'keyword'}`を集め、`hasKeyword`がそれを参照、実ゲーム処理（ブロッカー/貫通/再起動/速攻等）が`hasKeyword`を使う。
- 旧テキスト解析（regexパターン群：1126-1130/7010-7450等）は**EDB未登録カードのフォールバック**。我々の作業＝フォールバックからEDBの明示エントリへ移すこと。**②キーワード付与はこのstatic機構を使うのでEDB方式のまま＝原則順守**。

### ★静的キーワード付与の形式（静的DP継承と同形）
```json
{ "kind":"static",
  "condition": { "and":[ {"isMyTurn":false}, {"exists":{"side":"my","zone":"battle","type":"デジモン","filter":{"colorAny":["青"]}}} ] },
  "grant": { "op":"keyword", "target":"self", "keywords":["ブロッカー"] } }
```
- `effects`に置く（このデジモン自身の静的効果）。`exists`は`edbSelectCandidates`で側/色/種別を完全サポート。`keywords`はJP名（「ブロッカー」「貫通」「ジャミング」）で格納（hasKeywordが`KEYWORD_JP`経由で照合）。
- 効くキーワード（hasKeyword参照を確認）：ブロッカー(9928/10066)・貫通(10084)・ジャミング(10166)・再起動(5568/10506)・速攻(9750/12639)。

### 登録3枚（いずれも `effects:[{kind:static, condition, grant:keyword}]`）
|id|内容|
|--|--|
|ST9-07|相手ターン＋青の自分デジモンがいる間 ブロッカー|
|ST10-07|相手ターン＋黄の自分デジモンがいる間 ブロッカー|
|BT2-026|自分ターン＋青の自分テイマーがいる間 ジャミング|

### 実証メモ（`test_b2_batch23` 8-0）
- `hasKeyword(inst,'blocker'/'jamming')`が**条件成立時のみtrue**を確認（=実ゲーム処理が見るのと同じ関数）。色該当がいない/ターンが違うとfalse。
- **次段の候補**：①triggered＋このターン付与「自分のデジモン1体は《貫通》を得る」(BT4-058等＝`grant_keyword`op→tempKeywords、selector対応の確認要)。②時限付与「次の相手のターン終了まで」(時限ストアの有無を調査)。③他デジモン/全デジモンへの付与(`target`をselector化)。

## 4m. このセッションの確定追加：B1家族【キーワード付与・誘発】第24弾（3枚・確定238→241）

静的付与(§4l)に続き、**誘発＋一時付与**を可能に。

### ★`grant_keyword`にpicker追加（既存EDB未使用→安全・回帰85/0維持）
従来は`self`か「全候補」しか付与できなかった（`edbSelectCandidates`はcount制限せず全件返すため）。destroyと同方式で**count指定時にpickerを開く**よう拡張：
```js
if (!p.selector || p.selector === 'self') { apply([self]); return; }
const items = edbSelectCandidates(sel,...).filter(kind=inst);
if (!count || (items.length<=count && !sel.upTo)) { apply(all); return; }
edbOpenPick(..., {count:min(count,len), skippable:!!sel.upTo}, seld=>apply(seld), ()=>next());
```
- これで `self`／`全体(count無し)`／`N体選択(count:N)`／`N体まで(count:N,upTo:true)` を網羅。
- **持続**：tempKeywordsは`doActivePhase`(自分のアクティブフェイズ開始=次の自分ターン頭)で消去。よって**自分ターン発動**なら「このターンの間」も「次の相手のターン終了まで」も機能的に充足（攻撃系=このターンのみ有効・残存無害／ブロッカー等=相手ターンで有効）。厳密な「このターンのみ(相手ターン前に消す)」は表現できないが、対象キーワードの性質上影響なし。

### 登録3枚（on_evolve＋grant_keyword selector）
|id|内容|selector|kw|
|--|--|--|--|
|BT4-058|このターン 自分1体 貫通|`{my,battle,デジモン,count:1}`|貫通|
|ST5-09|次相手ターン終了まで 自分1体 ブロッカー|`count:1`|ブロッカー|
|ST5-12|次相手ターン終了まで 自分2体まで 再起動|`count:2,upTo:true`|再起動|

### 実証メモ（`test_b2_batch24` 6-0）
- pickerで**選んだ対象のみ**にキーワードが付き(hasKeyword=true)、非選択はfalse。1体・2体までとも確認。
- **次段**：①静的「全デジモンは《X》を得る」等`target`がself以外の静的付与→`hasKeyword`が`g.grant.target==='self'`しか見ないので拡張要(target selectorをinstに照合)。②`grant_status`(消滅しない等の状態付与)・置換効果。③Sアタック+N付与(セキュリティアタック)。

## 4n. このセッションの確定追加：B1家族【キーワード付与・静的全体】第25弾（1枚・確定241→242）

キーワード付与の3形態目（**静的・全体/フィルタ付**）を可能に。これでキーワード付与は **静的自己(§4l)／誘発N体選択(§4m)／静的全体(本節)** を網羅。

### ★`hasKeyword`のtargetをselector対応に拡張（既存EDB未使用→回帰85/0維持）
従来は`target:'self'`の付与しか効かなかった。targetがselectorオブジェクトの場合、**instが付与者から見た対象集合に含まれるか**を照合する分岐を追加：
```js
if (_tgt === 'self' || !_tgt) _ap = (g.inst.instanceId === inst.instanceId);
else if (typeof _tgt === 'object') {
  try { _ap = edbSelectCandidates(_tgt, g.inst, {inst:g.inst}).some(it => it.kind==='inst' && it.inst.instanceId===inst.instanceId); } catch(e){ _ap=false; }
}
```
- `edbActiveGrants`は静的ブロックの`condition`を既にチェック済みなので、ここではtarget照合のみ。
- これで「(色/名称/特徴/Lv)自分のデジモン全ては《X》を得る」が表現可能。`grant.target`にselectorを入れる。

### 登録1枚（`effects:[{static,condition,grant}, {triggered on_play}]`）
- **BT6-082**：condition=`or[exists(nameContains:[ハックモン]), exists(featureAny:[ロイヤルナイツ])]`（「か」=OR）、grant=`{op:keyword, target:{my,battle,デジモン,filter:{nameContains:[シスタモン]}}, keywords:[ブロッカー]}`、＋on_play draw1。「お互いのターン」=isMyTurn条件なし。

### 実証メモ（`test_b2_batch25` 7-0）
- シスタモン名の**全員**にブロッカー付与（非シスタモンは無し）、OR条件（ハックモン名orロイヤルナイツ特徴）、条件不在で無付与、登場時ドローを確認。
- **キーワード付与の残課題**：①`セキュリティアタック+N`付与＝`getSatk`(5344〜)が`tempSatk`＋旧効果のみ参照しEDB付与を見ない→getSatk拡張 or 静的satkボーナス機構が要る。②`grant_status`(消滅しない等)＝destroy等が`grantedStatus`を見るか要調査(置換効果)。③時限「次の相手ターン終了まで」の厳密化(現状tempKeywords近似)。

---

## 5. ★最重要：発見した既存不具合（私のバッチとは無関係・要対応）

### 5-1. EX12-073「巨大肉」が**孤児参照**＝クラッシュ源
- **事実**：`effects-db-version-Opus.json` に EX12-073 エントリと `_meta._ex12073` メモがあり、`test_ex12073-version-Opus.js` も参照している。**しかし EX12-073 のカードデータは cards.json にも card-overrides.json にも存在しない**（現 overrides は BT20-072/BT22-064/BT22-073/P-209/ST22-06 の5件のみ）。
- **機構**：`getCard('EX12-073')` が **null** → `confirmPlayOption`（6056行 `const card=getCard(cardId)` → 6059行 `card.name`）で **TypeError クラッシュ**。`test_security_flip` も EX12-073 を場に置けず 5-1。
- **由来**：過去セッションで EX12-073 を「cards.json欠落のため card-overrides.json に新規登録」した建付け（`_meta._ex12073` 参照）だが、**その override データが現ファイルに無い**＝ファイル同期/納品漏れ（このプロジェクト既知の事故パターン）。`_meta._ex12073` メモ自体が「test_ex12073 7-0」と書くが**現状は成立しない**。
- **selectできる対応（お客様判断）**：
  - (A) EX12-073 の**カードデータ（override）を復元**する（お客様が原データをお持ちなら card-overrides.json に再追加。`id` フィールドを付ければ 1744-1746行が新規カードとして実体化する）。復元後 `test_ex12073` / `test_security_flip` が通るはず。
  - (B) EX12-073 を**ドロップ**するなら、EDBの `EX12-073` エントリ・`_meta._ex12073`・`test_ex12073-version-Opus.js` を削除し、孤児参照を消す。
  - ※どちらも**カード内容の取捨＝お客様の領分**なので、本セッションでは独断で改変せず**温存＋明記**に留めた。
- **任意の堅牢化（未実施・候補）**：`confirmPlayOption` 冒頭に `if(!card){log('カードデータ無し:'+cardId);return;}` を足せば、データ欠落カードでも**ターンごとクラッシュしなくなる**（GPT-5.5堅牢化監査の残項目とも整合）。ただしEX12-073が「動く」ようにはならない（データ復元が別途必要）。

### 5-2. LM-Scrambleの検証ファイルが見当たらない
- EDBの LM-027 `_memo` は「検証=test_lm_scramble-version-Opus.js」と書くが、**そのファイルがプロジェクトに無い**（納品漏れの可能性）。LM-027〜032 自体は cards.json に実在し、`costReduction` 付き evolve は実装済み（18191行〜）。動作確認は実機 or 新規テストで補完を。

---

## 6. 新しい目線で発見したエンジン上の論点（記録・大半は据え置き）

1. **`extractKeywords` の末尾キーワード脱落（実バグ・広域影響）**：1575行 `if (REF_NEXT.includes(next))` で、キーワードが文末にあると `next=''`、JSの `'をがとや…'.includes('')` は**常に true** → そのキーワードを**抽出しない**。よって `evolved_effect` が「再起動」「ブロッカー」等の**単独末尾キーワードのみ**のカードは、進化元キーワードが**レガシー・EDB双方で非機能**（ST15-10/EX8-049のinheritedを `kind:'rule'`(no-op) で記録しているのはこのため＝**退行ではなく既存限界の明記**）。
   - 影響：effect/evolved_effectが末尾キーワードで終わる多数カードでキーワードが落ちる。**外科的修正は `if (next!=='' && REF_NEXT.includes(next))`** だが、**多数カードの挙動が一斉に変わる**ため、B1案件として**回帰85-0を確認しつつ**慎重に。今回は据え置き。
2. **parallel `-P` の明示エントリは冗長**（5-2の `edbFx` フォールバックにより base だけで足りる）。今後 parallel を個別登録する必要は無い。既存の冗長`-P`は無害なので削除も急がない。
3. **`turn_start`（自分のターン開始時）は永久不発**（EDB側に発火呼び出しが無い）。`main_start`（自分側）も自動発火未確認。**これらトリガに効果を入れない**。
4. セキュリティ：security効果が実機フリップで動くのは**EDB登録済みオプションのみ**（FIX#3＝守備側視点で自動解決、`_meta._engine_fixes_opus` 参照）。memory/satk操作は側入替対象外なので、security内でmemory/satkを動かすカードはフリップ時に攻撃側視点で解決され得る（限界）。

---

## 7. 設計上の鉄則（破ると事故る）

1. **検証前ドラフトは実行しない**（`_verified:false`/`_draft:true`。`edbFx` 17110行付近のゲートで停止し従来パーサにフォールバック）。
2. **全ブロック被覆ゲート**：1枚の効果ブロックを「一部だけ」DSL化しない（**EDBは排他ルーティング**＝EDB登録カードはレガシーパーサを通らない。残った節は静かに消える）。effect/evolved_effect/security_effect の**全部**を被覆するか、被覆できないカードは保留。
3. **`_warn`無し＝安全ではない**。必ず**原文と1対1**で突き合わせる。
4. **`effect` と `evolved_effect` は別物**：本体effect→`effects:[]`、進化元effect→`inherited:[]`（自分が進化元のとき発火）。**evolved_effectをeffectsに入れると誤発火**。
5. **EDBで“実発火”するトリガだけ使う**：`on_play / on_evolve / on_attack / on_destroy / turn_end系 / on_move / security / main(手動)` ほか。**`turn_start` は不発**。
6. **条件付き/新op は機能テストする**（DSL検査だけでは不十分。例：`lvIs:7` は lv=null で永久不発。今回の `trash_source` も初使用なので専用テストを作った）。
7. **確定済み(verified)は上書きしない**。**元データ（cards.json / card-overrides.json）の参照名は変えない**。私の成果物は `version-Opus` を付与。
8. **1段ごとに**：（必要なら変換器再生成→）validate(確定/ドラフト)0エラー→ test_edb 85-0 → 機能テスト → 原文サンプル照合 → **本書更新** → **`present_files` で実ファイル納品（最後に必ず）**。

---

## 8. op/フィルタ語彙（実証メモ・次担当の即戦力）

- 対象op既定：`destroy/bounce/devolve/rest/trash_source` の selector 既定＝相手1体・複数候補picker・0体不発。`bounce` の戻し先 `to` 既定 `'hand'`。
- フィルタ（`edbCardMatchesFilter`）：「Lv.N以下/以上/Lv.Nの」→`lvMax/lvMin/lvIs`、「コストN以下/以上」→`costMax/costMin`、「DP N以下/以上」→`dpMax/dpMin`（getDPで現在値）、型→`type`、特徴→`featureAny/featureAll/featureContains`（検査器は `featureAny`(exact)推奨）、色→`colorAny`、名称→`nameContains/nameIs`、「アクティブ/レスト状態の」→ selector直下 `rested:false/true`。
- **「N体まで」等の複数対象**：destroy/bounce/devolve/trash_source は **1体しか選ばない**ため不可（保留）。
- reveal帯：`reveal_deck_top`→`select_from_revealed{selector,to:'hand',upTo:true}`→`dispose_revealed{to:'deck_bottom'|'trash'}`。
- 進化（コスト軽減）：`evolve{... , costReduction:N}`＝`getEvoCost`で正規進化コストを算出し-N（min0）、`canEvolveOnto`準拠の組合せのみ。未指定は従来固定cost。
- トラッシュ回収：`move{from:'trash',to:'hand',side:'my',count,type,filter}`。
- オプション：本体は `effects:[{trigger:'main',…}]`、セキュリティは `security:[{trigger:'security',…}]`。「セキュリティ：このカードのメイン効果を発揮する」は**securityにmainと同一actionsを複製**（発火文脈が別＝二重発火しない）。

---

## 9. 来歴 / ファイル境界

- 本体エンジン大部分・effects-db 初期15枚・回帰85件＝**Fable5**。UI（プレマ透過/裏面）・P2基盤・確定昇格・カード画像・各エンジン修正（ターン終了/security発火/フリップ/costReduction）＝**Opus**（`_meta._provenance` と各エントリ `_memo` の `(Opus)` で区別）。
- **境界**：Opusが作成/改変した全ファイルに `version-Opus`。お客様の元データ **cards.json / card-overrides.json は無改変＝元名のまま**。

---

## 10. ★戦略：B2クリーン枯渇の実証と「同時10パターン」を実現する加速策

お客様の方針＝**「類似効果が少ない（レア/多様な）パターンを、できれば1バッチ約10種ずつ」**。これに対する**実証データ**と推奨を残す。

### 10-1. 実証：現op群で「忠実かつ完全」に書ける**無条件**札はほぼ枯渇
- 未カバー（base未登録）で effectあり ＝ 5,891枚 / 正規化パターン 3,303種（うち1枚のみ＝1,733種）。だが**忠実に書ける**かは別問題。
- 厳格スキャン（単文・単一対象・発火トリガ・地雷語ゼロ）の結果、**evolved/sec無しの無条件クリーン札は第12弾でほぼ尽きた**。残る単文クリーン札（16枚/10sig）は**全て進化元効果を併せ持ち、その大半が「ターン1回」「消滅置換」「条件付きサブトリガ」で表現不能**。完全被覆できたのは第13弾の3枚のみ。
- **機械分類は信用不可（再実証）**：緩いスキャンが出した「クリーン候補」を原文照合したら、**「〜することで（コスト払い）」「ブロッカーを持つ（KWフィルタ）」「進化元を持たない（無進化元フィルタ）」「登場時◯◯消滅時△△（複数ブロック連結）」が大量に混入**。信じて一括登録していたら不忠実エントリを15枚出荷していた。**必ず原文1対1＋機能テスト**。

### 10-2. 結論：純B2のみで「10種/バッチ」は持続不可。**小さなB1op追加が量産の正攻法**
1opまたは1フィルタを足すと**一族（数十〜数百枚）がまとめて表現可能**になる。お客様は以前B1を出力負担で後回しにされたが、**「10種ずつ」という量目標にはB1が最短**。以下は**小さく安全（外科的）で高レバレッジ**な候補（推奨順）：

1. **コスト払い『〜することで』family の解禁（新op不要・最大の山）**：EDBは既にコスト機構を持つ（ST14-11＝手札破棄コスト、LM-Scramble＝レストコスト、`edbResolvePending` の支払い確認）。「自分の手札N枚破棄することで」「自分のデジモン1体消滅させることで」等を**既存コスト機構へ写像できるか**をまず1枚で実証→できれば巨大familyが開く。**次セッションの最優先調査**。
2. **`noSource`（無進化元）フィルタ**：`edbSelectCandidates` に `if(sel.noSource && (i.stack||[]).length>0) continue;` を1行追加。「進化元を持たない相手1体を…」family（BT6-026/EX2-014 等）が開く。
3. **キーワードフィルタ `keyword`**：`edbCardMatchesFilter` に `if(f.keyword && !hasKeyword(c,f.keyword)) return false;` を追加（hasKeywordはJP/内部両対応・5343付近）。「ブロッカー/ブロッカーを持つ相手を消滅」family（BT1-023 等）。※静的キーワードがcard.keywordsに載るものに限る。
4. **複数対象 up-to-N**：destroy/bounce/rest/devolve の picker を `count:N` 選択可に（現在1体固定）。「Lv.N以下の相手2体までを手札に戻す」family（大）。`edbOpenPick` は既にcount対応なので、op側の `count` を selector から拾うだけ。
5. **多型対象（type配列）**：`edbCardMatchesFilter` の `type` を配列許容に（`Array.isArray(f.type)?f.type.includes(c.type):c.type===f.type`）。「デジモン/テイマー1体を消滅」family。

> いずれも**回帰85-0を維持しながら**1つずつ。**op追加→専用機能テスト→既存不変diff→本書更新**の順（鉄則§7）。1opごとに「解禁された一族」をB2で収穫すれば、**実質10種/バッチ以上**が現実的になる。

### 10-3. 当面（B1を採らない場合）のB2継続ネタ
- 条件付き（exists/zoneCount）×filter（lvMax/lvMin/lvIs/dpMax/costMax）の destroy/rest/bounce/devolve で、**evolved/sec無し or 継承が静的DP/単純アタック時**のもの。第13弾の要領で**両分岐を機能テスト**して少数ずつ。
- 「相手1体を手札/デッキに戻す（進化元は破棄）」複合は **bounceだけで足りる**（`edbMoveInstTo` が移動時に進化元を自動トラッシュ＝17755行。デジモンTCGの離場ルールと一致）。ただし条件・複数対象が付く版は上記B1が要る。

### 10-4. ★現op群（B1なし）で表現できることの確定リスト（第13・14弾で実証）
「条件・回数制限・逐次・同時誘発」は**op追加なしで**書ける。これらを使う札は**B2**（優先対象）。
- **条件**（`effects[].condition` / ブロック単位）：`exists`/`notExists`（盤面の存在・色/型/特徴/名称フィルタ可）、`zoneCount`（**security/trash/hand/battle**の枚数を`op:'>='|'<='|'>'|'<'|'=='`で比較）、`unitCount`（型/フィルタ/`rested`/`noSource`で数える）、`memory`（メモリー値）、`self`（自身の特徴等）、`selfKeyword`、`isMyTurn`、論理`and/or/not`。
- **回数制限**：`"limit":"once_per_turn"`（ターン1回。EX5-062が実例）。
- **逐次（その後）**：`actions:[ ... ]` は順次実行＝「Aする。その後Bする」を1ブロックで（A・Bが現opなら可）。
- **条件分岐（XならA／YならB）**：**同一トリガの複数ブロック**に分けて各々に`condition`。境界で両成立なら**同時誘発モーダル**で両方発揮（ルール通り）。
- **未活用だが使える主なop**：`activate`（対象選択・レスト対象のみ）、`dp_mod`（selector・このターン）、`satk_mod`（**自身のみ**）、`bounce`（`to:hand/deck_top/deck_bottom`・移動時に進化元自動トラッシュ）、`mill`（**自分の**デッキ）、`recovery`、`draw`、`memory`(±)、`devolve`、`trash_source`、`rest`/`rest_self`/`activate_self`、`move`(trash→hand 等)、`evolve`(costReduction)。

### 10-5. ★「希少（類似が少ない）パターンを優先」の狙い（お客様方針・記録）
- **狙い① 出力の軽さ**：1パターンの該当枚数が少ない＝1バッチの出力が軽く、レビューも安全。
- **狙い② 多様な札を重ねることで原始要素（op/条件/フィルタ）が揃い、よくある型も結果的にカバーされていく**という期待。
- **エンジニア注記（正直ベース）**：EDBは**カード単位の明示登録**なので、希少札をやっても**よくある札が自動登録されるわけではない**（各カードに個別エントリが要る）。ただし狙い②は方向性として正しい——多様な希少札を処理すると**必要なop・条件・フィルタの語彙と実証が蓄積**し、後で「よくある一族」をB1で一括登録する際の**リスクが激減し高速化**する。つまり希少優先は「**量産前の道具箱づくり＋出力の平準化**」として機能する。よって当面は希少・多様（現op完結）をB2で淡々と、頻出・op依存はB1として保留、という現方針が妥当。

### 10-6. B1（後回し）と判定した「op追加で一族解禁」型の確定リスト
次のいずれかを含む札は**B1**＝今は手を付けない（将来opを足す時にまとめて）：
1. **コスト払い「〜することで」**（手札破棄/自デジモン消滅 等を払って効果）＝最大の山。※既存コスト機構（`rest_self`/`discard_hand`/`edbResolvePending`）で書ける可能性が高く、**次の最優先調査**。
2. **キーワードフィルタ**（「ブロッカーを持つ相手」等）＝`edbCardMatchesFilter`に`keyword`未実装。
3. **無進化元フィルタ（対象側）**（「進化元を持たない相手」）＝`edbSelectCandidates`に対象用`noSource`未実装（条件側`unitCount.noSource`は有る）。
4. **複数対象「N体／N体まで／合計コストN」**＝picker count拡張で一族解禁。
5. **多型対象「デジモン/テイマー」**＝`filter.type`の配列許容で解禁。
6. **`satk_mod`の対他・対自他**（「相手1体にSアタック-N」「自分1体はSアタック+N」）＝satk_modが自身固定。selector化で解禁。
7. **置換・防止・付与の一部**（「消滅しない」「アタックできない」「〜として扱う」「トークン生成」等）＝対応opが無い（一部は新op必要）。

### 10-7. ★第15弾で判明した「条件の壁」＝最有力のB1（条件追加で進化ライン札が大量解禁）
無条件クリーン札の枯渇後、希少札を阻むのは**op不足より「条件語彙の不足」**だった。以下の**条件キーを足す**と、それぞれ大きな一族が解禁される（現条件評価`edbCheckCondition`に分岐を追加するだけ＝外科的）。**B2を続ける上での最優先B1候補**。
1. **`selfSource`（自分の進化元に名称/特徴Xがある）✅【第16弾で実装済】**＝最大の山。「このデジモンの進化元に「アグモン」があるとき」「進化元に名称『メタルグレイモン』を含む」等、**進化ライン札の典型**。`inst.stack` を走査して名称/特徴一致を見る（§4e）。**約120枚が解禁済み**（個別被覆はこれから・特殊進化等の別要素に注意）。
2. **`attackTarget:'player'`（プレイヤーにアタックしたとき）**＝頻出。エンジンは既に `_attackTarget`（player/digimon）を保持（9827付近）。on_attackブロックに`attackTarget`条件を読ませるだけ。BT2-015/BT2-019 ほか。
3. **`destroyedBy:'effect'`（効果で消滅していたなら）**＝消滅時札の一部。消滅トリガに破壊原因フラグを渡す必要あり（中規模）。BT5-071 ほか。
4. **`playedFrom:'source'`（進化元から登場していたなら）**＝on_playの分岐。BT7-018 ほか。

> これらは「op追加で量産」ではなく「**条件1個で一族解禁**」型。お客様の現方針（op追加=B1は後回し）に厳密には該当するが、**条件追加は影響範囲が読みやすく回帰リスクが低い**（既存カードの挙動を変えない＝新規登録時のみ効く）。希少B2が細ってきたら、まず **selfSource** を入れるのが費用対効果最大。
