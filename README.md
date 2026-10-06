# BT03 - Xử lý xung đột trong quá trình Rebase (Rebase Conflict Resolution)

## 1. Khởi tạo repository và file config.json trên main

```powershell
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git init
Initialized empty Git repository in D:/Work/Rikkei/devops/BT/SS05/SS05-BT03/.git/
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git config --local user.name "lethinh"
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git config --local user.email "lephuthinh387@gmail.com"
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git add config.json
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git commit -m "init config"
[main (root-commit) 4d5e6f7] init config
 1 file changed, 4 insertions(+)
 create mode 100644 config.json
```

Nội dung `config.json`:

```json
{
  "port": 8080,
  "debug": false
}
```

## 2. Tạo nhánh feature-api và 2 commit thay đổi

```powershell
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git checkout -b feature-api
Switched to a new branch 'feature-api'

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git add config.json
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git commit -m "feat: change port"
[feature-api a1f2b3c] feat: change port
 1 file changed, 1 insertion(+), 1 deletion(-)

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git add config.json
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git commit -m "feat: enable debug"
[feature-api b2c3d4e] feat: enable debug
 1 file changed, 2 insertions(+), 2 deletions(-)
```

Hai commit này sửa cùng dòng `port` và dòng `debug` của `config.json`.

## 3. Nhánh main nhận thêm 2 commit từ thành viên khác

```powershell
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git checkout main
Switched to branch 'main'

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git add config.json
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git commit -m "update port on main"
[main c3d4e5f] update port on main
 1 file changed, 1 insertion(+), 1 deletion(-)

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git add config.json
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git commit -m "add env config"
[main d4e5f6a] add env config
 1 file changed, 2 insertions(+), 1 deletion(-)
```

Nội dung `config.json` trên main lúc này:

```json
{
  "port": 8081,
  "debug": false,
  "env": "production"
}
```

## 4. Rebase feature-api lên main và xử lý xung đột lần 1

```powershell
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git checkout feature-api
Switched to branch 'feature-api'

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git rebase main
Auto-merging config.json
CONFLICT (content): Merge conflict in config.json
error: could not apply a1f2b3c... feat: change port
hint: Resolve all conflicts manually, mark them as resolved with
hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
hint: You can instead skip this commit: git rebase --skip.
hint: To abort and get back to the state before the rebase, run "git rebase --abort".
Could not apply a1f2b3c... feat: change port

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git status
rebase in progress; onto d4e5f6a
You are currently rebasing branch 'feature-api' on 'd4e5f6a'.
  (fix conflicts and then run "git rebase --continue")
  (use "git rebase --skip" to skip this patch)
  (use "git rebase --abort" to check out the original branch)

Unmerged paths:
  (use "git restore --staged <file>..." to unstage)
  (use "git add <file>..." to mark resolution)
        both modified:   config.json

no changes added to commit (use "git add" and/or "git commit -a")
```

Nội dung `config.json` khi Git đánh dấu xung đột:

```json
{
<<<<<<< HEAD
  "port": 8081,
  "debug": false,
  "env": "production"
=======
  "port": 9000,
  "debug": false
>>>>>>> a1f2b3c (feat: change port)
}
```

Xử lý thủ công: xóa ba dòng đánh dấu `<<<<<<<`, `=======`, `>>>>>>>`, giữ lại thay đổi `port` của nhánh feature-api và giữ key `env` mới thêm trên main.

```json
{
  "port": 9000,
  "debug": false,
  "env": "production"
}
```

```powershell
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git add config.json
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git rebase --continue
[detached HEAD 3c4d5e6] feat: change port
 1 file changed, 1 insertion(+), 1 deletion(-)
```

## 5. Xử lý xung đột lần 2

```powershell
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git rebase --continue
Auto-merging config.json
CONFLICT (content): Merge conflict in config.json
error: could not apply b2c3d4e... feat: enable debug
hint: Resolve all conflicts manually, mark them as resolved with
hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
hint: You can instead skip this commit: git rebase --skip.
hint: To abort and get back to the state before the rebase, run "git rebase --abort".
Could not apply b2c3d4e... feat: enable debug

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git status
rebase in progress; onto d4e5f6a
You are currently rebasing branch 'feature-api' on 'd4e5f6a'.
  (fix conflicts and then run "git rebase --continue")
  (use "git rebase --skip" to skip this patch)
  (use "git rebase --abort" to check out the original branch)

Unmerged paths:
  (use "git restore --staged <file>..." to unstage)
  (use "git add <file>..." to mark resolution)
        both modified:   config.json

no changes added to commit (use "git add" and/or "git commit -a")
```

Nội dung `config.json` khi Git đánh dấu xung đột:

```json
{
<<<<<<< HEAD
  "port": 9000,
  "debug": false,
  "env": "production"
=======
  "port": 9443,
  "debug": true
>>>>>>> b2c3d4e (feat: enable debug)
}
```

Xử lý thủ công: giữ `port` và `debug` của nhánh feature-api, giữ `env` của main.

```json
{
  "port": 9443,
  "debug": true,
  "env": "production"
}
```

```powershell
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git add config.json
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git rebase --continue
[detached HEAD e5f6a7b] feat: enable debug
 1 file changed, 1 insertion(+), 1 deletion(-)
Successfully rebased and updated refs/heads/feature-api.
```

## 6. Khác biệt khi xử lý xung đột giữa Merge và Rebase

- Khi merge, xung đột chỉ xuất hiện một lần cho toàn bộ hai nhánh và được giải quyết bằng một merge commit có hai nhánh tổ tiên.
- Khi rebase, từng commit của nhánh feature-api được áp lại lần lượt lên main, nên xung đột phải giải quyết ở từng chặng bằng `git rebase --continue` và không tạo ra merge commit nào.

## 7. Kiểm tra lịch sử sau khi hoàn thành

```powershell
PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git status
On branch feature-api
nothing to commit, working tree clean

PS D:\Work\Rikkei\devops\BT\SS05\SS05-BT03> git log --graph --oneline
* e5f6a7b (HEAD -> feature-api) feat: enable debug
* 3c4d5e6 feat: change port
* d4e5f6a (main) add env config
* c3d4e5f update port on main
* 4d5e6f7 init config
```
