`git add <文件名称>`

`git add .`

`git status`

`git commit -m 'xxxxxxxx'`

`git push`

`git config --global core.autocrlf true`

---

如果是在windows上的git bash上使用vim编辑文件后会出现一些警告 例如：

> $ git add . 
> warning: in the working copy of '.bashrc', LF will be replaced by CRLF the next time Git touches it

### 警告的意义
这个警告意味着Git检测到你的`.bashrc`文件的行尾结束符（line ending）与预期配置不符。Windows系统使用回车符加换行符（CRLF）作为行结束符，而Unix/Linux系统使用换行符（LF）。

### 为什么会出现这种情况
这通常是由于Git的`core.autocrlf`配置引起的。这个配置控制Git如何处理文本文件中的行结束符，目的是为了避免在不同操作系统间共享文件时出现问题。

- **`core.autocrlf = true`**：在检出文本文件时将LF转换为CRLF，在提交时将CRLF转换为LF。这通常推荐给Windows用户，以防在期望LF的Unix/Linux环境中出现问题。
- **`core.autocrlf = input`**：确保文件以LF提交，但检出时不做转换。这通常推荐给Unix/Linux/Mac用户。
- **`core.autocrlf = false`**：不进行自动转换。这种设置较少见，如果不恰当配置可能导致问题。

### 解决方法
1. **根据你的操作系统和需求设置`core.autocrlf`**：
   - Windows用户：`git config --global core.autocrlf true`
   - Unix/Linux/Mac用户：`git config --global core.autocrlf input`
   
2. **如果当前行为是你所期望的，或者不会在你的工作流中造成问题，可以忽略这个警告**。

3. **规范化仓库**：
   - 你可以通过运行以下命令来规范化仓库中的文件行结束符：
     ```
     git add --renormalize .
     ```
   - 这个命令会根据你的`core.autocrlf`设置重新规范化仓库中的所有文件。

选择最适合你的开发环境和协作需求的选项。如果你与使用不同操作系统的其他人协作，通常建议设置一个能防止行结束符问题的`core.autocrlf`策略。

通常使用`git config --global core.autocrlf true`就好了
