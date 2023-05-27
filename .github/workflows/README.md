# Github action

## 1 自动回复

- 这个action用于监听 issue 和 pull request 的新评论并自动回复。代码如下：

```yaml
name: auto-reply-comment

on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]

jobs:
  auto-reply:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Install dependencies
      run: npm install @octokit/rest

    - name: Auto-reply comment
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: |
        const { Octokit } = require("@octokit/rest");
        const octokit = new Octokit({ auth: process.env.GITHUB_TOKEN });
        const { issue, comment, pull_request } = github.context.payload;
        const issueNumber = issue ? issue.number : pull_request.number;
        const repo = github.context.payload.repository.full_name;

        const reply = "Thank you for your comment! We will get back to you as soon as possible.";

        octokit.issues.createComment({
          repo,
          issue_number: issueNumber,
          body: reply,
          headers: {
            accept: "application/vnd.github.squirrel-girl-preview+json"
         }
        });
  ```
  
- 这个 Action 监听 issue_comment 和 pull_request_review_comment 事件的 created 类型，也就是只要有新的评论就会触发。
- 当事件触发后，它会检查触发事件是针对 issue 还是 pull request，并获取对应的 issue 或 pull request 编号和仓库名称。
- 然后，会使用 Octokit 库创建一个新的评论，以回复收到的评论。
- 最后，将这个 Action 添加到你的仓库中。具体来说，在仓库中创建一个 .github/workflows 目录，并在其中创建一个名为 auto-reply-comment.yml 的 YAML 文件。将上面的代码复制到该文件中，并提交到仓库中，这样就可以启用这个 Action 了。

- 要添加新的 Actions secrets，按照以下步骤操作：

1. 打开仓库的页面，然后点击页面上方的 Settings 按钮；
2. 在页面左侧的菜单中，点击 Secrets；
3. 点击 New repository secret 按钮；
4. 输入你的 secret 的名称和值；
5. 点击 Add secret 按钮。


## 2 issue和pr的自动回复

- 创建的思路与上述相同，此处不再赘述。
- 代码如下：

```yaml
name: Greetings

on: [pull_request_target, issues]

jobs:
  greeting:
    runs-on: ubuntu-latest
    permissions:
      issues: write
      pull-requests: write
    steps:
    - uses: actions/first-interaction@v1
      with:
        repo-token: ${{ secrets.GREETINGS }}
        issue-message: "Thank you for your issue! We will get back to you as soon as possible."
        pr-message: "Thank you for your PR! We will get back to you as soon as possible."
```

