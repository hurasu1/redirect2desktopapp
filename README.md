# redirect2desktopapp
https://claudeなどできたものをclaude://に転送する。あ

## 使い方

| 開くもの | リンク |
|---|---|
| Claude | `?to=claude&q=プロンプト` |
| ChatGPT | `?to=chatgpt&q=プロンプト` |
| Asana / Notion / Slack | `?to=asana&url=元のhttpsリンク`（notion・slack も同じ） |
| VS Code（フォルダ・ファイル） | `?to=vscode&path=/絶対パス`、行を指定するなら `&line=10` |
| VS Code（SSH 先） | `?to=vscode&host=10.10.0.119&path=/絶対パス`（host は `~/.ssh/config` の Host 名か IP） |
| Claude Code のセッションの再開 | `?to=claude-resume&session=<UUID>&path=/作業フォルダ&host=dlbox3`（host は省略可） |

VS Code は `vscode://file/<パス>`、host があれば `vscode://vscode-remote/ssh-remote+<host><パス>`（Remote-SSH）に転送する。どちらも末尾に `?windowId=_blank` を付けて、新しいウィンドウで開く。SSH 先では行番号の指定は使わない。絶対パスだけ受け付け、`..` を含むものは開かない。

セッションの再開は、ボタン1回で再開のコマンドをクリップボードにコピーし（ブラウザはボタンを押さないとコピーさせない）、host があればそのまま VS Code（Remote-SSH）で作業フォルダを新しいウィンドウで開く。VS Code のターミナルに貼れば `claude --resume <UUID>` で再開できる。host がなければ Claude デスクトップで開くコマンド（`claude --desktop --resume <UUID>`）をコピーする。Claude Code 拡張のパネル（`vscode://anthropic.claude-code/open?session=`）は、claude -p のセッションを開けなかったので使わない。ai-ops の `scripts/run_task.py` が、台帳のタスクの「AIセッション」にこのリンクを入れる。

スマホでは、Claude（`to=claude`）を自動で開かず、依頼文とボタンを2つ出す。「コピーして Claude Code を開く」は依頼文をコピーして `https://claude.ai/code` を開く（PC で常駐させた `claude remote-control` の環境を選び、新しいセッションに貼る）。「チャットで開く」は `https://claude.ai/new?q=` を開く。VS Code とセッションの再開は、スマホでは開けないと案内する。PC で確かめるときは `&mobile=1` を付ける。

アプリに渡すだけの転送（Claude・ChatGPT・Asana・Notion・Slack・VS Code）は、3秒後にタブを閉じる。ブラウザが閉じさせない場合（リンクから新しく開いたタブでないとき）は残る。セッションの再開・内容の確認・エラー・ブラウザで開き直すときは閉じない。

## GitHub Pages deployment

Before the first deployment, a repository administrator must enable Pages in
**Settings → Pages → Build and deployment → Source → GitHub Actions**.
Then run **Deploy to GitHub Pages** from the Actions tab, or push to `main`.
Without this setup, `actions/configure-pages` fails with `Get Pages site failed`
and `Not Found`.

Alternatively, add a repository Actions secret named `PAGES_SETUP_TOKEN` containing
a fine-grained personal access token with access to this repository and
**Pages: Read and write** permission. The workflow uses it to enable Pages
automatically. The default `GITHUB_TOKEN` cannot perform this initial enablement,
even with `pages: write`. Remove the setup secret after Pages is enabled; subsequent
deployments use `GITHUB_TOKEN`.

スマホで AI（`to=claude`）を開くとき、`&pc=https://<名前>.ts.net` があれば、まずそこ（ai-ops の `task serve` を Tailscale で見せたもの）に回す。依頼文の中の Notion のタスクの URL を `/open?url=` で渡すと、PC がそのタスクでセッションを立て、そのセッション（claude.ai/code）に飛ばす。4秒たってもつながらなければ（スマホの Tailscale がオフ、PC が止まっている）、今までどおり「コピーして Claude Code を開く」「チャットで開く」のボタンを出す。`.ts.net` 以外には回さない。

セッションの再開（`to=claude-resume`）も、スマホで `&pc=` があり `host` がなければ、PC のサーバーの `/resume?session=<UUID>` に回す。PC はそのセッション（ワーカーの `claude -p` など）を続きとして Remote Control つきで起動し、そのセッションを開くボタンを出す。SSH 先のセッションはまだ回さない。
