---
description: Codex（gpt-image-2）で画像を生成・編集してプロジェクトに保存する
argument-hint: <作りたい画像の説明> [参考・編集元の画像パス]
allowed-tools: Bash(codex exec:*), Bash(ls:*), Bash(mkdir:*)
---

Codex CLI の組み込み `$imagegen` スキルを使って画像を生成・編集する。

依頼内容: $ARGUMENTS

手順:

1. 依頼内容から次を決める。
   - 保存先: 指定がなければ `assets/generated/` に、内容がわかる英語のファイル名（例: `orane-logo-v1.png`）で保存する。既存ファイルは上書きせず `-v2` などを付ける。
   - 依頼内容に既存の画像パスが含まれていれば、`-i <パス>` で Codex に渡す（参考画像・編集元として）。
2. `mkdir -p` で保存先フォルダを作る。
3. 次の形で Codex を実行する。プロンプトはシェル展開されないよう、必ずクォート付きヒアドキュメントで標準入力から渡す。

   ```bash
   codex exec -s workspace-write [-i <画像パス>] - <<'CODEX_PROMPT'
   $imagegen <依頼内容をそのまま、具体的に>
   生成した最終画像を <保存先パス> にコピーして保存してください。
   CODEX_PROMPT
   ```

4. 終わったら `ls -l` で保存されたファイルを確認し、パスをユーザーに伝える。画像を Read で開いて内容を一言で説明する。
5. 失敗した場合は Codex のエラーをそのまま見せる。ログインしていないエラーなら `/codex:setup` か `!codex login` を案内する。勝手に API キー経由の CLI モードへ切り替えない。
