Annotate.el [![MELPA][mi]][m] [![MELPA-STABLE][msi]][ms]
===========

[mi]: http://melpa.org/packages/annotate-badge.svg
[m]: http://melpa.org/#/annotate
[msi]: http://stable.melpa.org/packages/annotate-badge.svg
[ms]: http://stable.melpa.org/#/annotate

This package provides a minor mode `annotate-mode`, which can add annotations to arbitrary files without changing the files themselves. This is very useful for code reviews. When `annotate-mode` is active, `C-c C-a` will create, edit, or delete annotations. 

![example-screenshot](https://raw.githubusercontent.com/bastibe/annotate.el/master/example.png)

With an active region, `C-c C-a` creates a new annotation for that region. With no active region, `C-c C-a` will create an annotation for the word under point. If point is on an annotated region, `C-c C-a` will edit that annotation instead of creating a new one. Clearing the annotation deletes them.

Use `C-c ]` to jump to the next annotation and `C-c [` to jump to the previous annotation.

All annotations are saved in `annotate-file` (`~/.annotations` by default).

Annotations can be exported `annotate-export-annotations` as commented unified diffs, like this:

![diff-example-screenshot](https://raw.githubusercontent.com/bastibe/annotate.el/master/diff-example.png)

Alternatively, they can be integrated `annotate-integrate-annotations` as comments into the current buffer, like this:

![integrate-example-screenshot](https://raw.githubusercontent.com/bastibe/annotate.el/master/integrate-example.png)

### Incompatibilities:

- annotations in org-mode source blocks will be underlined, but the annotations don't show up. This is likely a fundamental incompatibility with the way source blocks are highlighted and the way annotations are displayed.

This package is released under the MIT license.

### Changelog

- **2015-06-12 V0.1 Bastian Bechtold**  
  First working release.

- **2015-06-12 V0.1.1 Bastian Bechtold**  
  Improve documentation and add license.

- **2015-06-12 V0.1.2 Bastian Bechtold**  
  Fix typo and version error.

- **2015-06-15 V0.1.3 Bastian Bechtold**  
  Improve README and auto-remove empty annotations created by earlier bug.

- **2015-06-15 V0.1.4 Bastian Bechtold**  
  Minor bug fixes.

- **2015-06-15 V0.1.5 Bastian Bechtold**  
  Annotations now work on long lines.

- **2015-06-19 V0.2.0 Bastian Bechtold**  
  Annotations can be exported as unified diff files.  
  Several smaller bug fixes.

- **2015-06-19 V0.2.1 Bastian Bechtold**  
  Now with fewer compile warnings (turns out, not really).

- **2015-06-19 V0.2.2 Bastian Bechtold**  
  Now with more compile warnings (0.2.1 didn't work).

- **2015-07-02 V0.2.3 Bastian Bechtold**  
  Can now disable minibuffer messages.

- **2015-09-17 V0.2.4 Bastian Bechtold**  
  Load and Clear don't mark buffer as modified any more.

- **2015-09-21 V0.3.0 Bastian Bechtold**  
  Add key bindings for jumping to next/previous annotation.

- **2015-09-22 V0.3.1 Bastian Bechtold**  
  Change key bindings for jumping as to conform with Emacs' standards.  
  Didn't change the main key binding though, because I don't know a good alternative.

- **2015-09-23 V0.4.0 Bastian Bechtold**  
  Completely reworked the display engine for annotations. You can now have several annotations per line, and annotations should not move any more when editing the line they are on. Finally, annotations can now span several lines.

- **2015-10-06 V0.4.3 Bastian Bechtold**  
  Bugfixes. No more hidden newlines, no more annotations in undo-list, no more error messages with annotations at bol, mark deactivated after creating annotation, annotations auto-reflow on frame size change.

- **2015-10-06 V0.4.4 Bastian Bechtold**  
  Added a new export system. Let's see if it turns out to be more useful than the previous one.
  
- **2016-08-25 V0.4.5 Bastian Bechtold**  
  Bugfix release for unicode annotations and multiline annotations.

- **2016-09-07 V0.4.6 Bastian Bechtold**  
  Bugfix release for annotations ending on an empty line.

- **2016-10-06 V0.4.7 Bastian Bechtold**  
  Bugfix release for buffers without a file name.
  
  


** my study
  课程0，理解

```
(defun annotate-create-annotation (start end &optional arg)
  "Create a new annotation for selected region."
  (interactive "r")
  (let* ((overlay-highlight (make-overlay start end))
         (eol (save-excursion (move-end-of-line nil) (point)))
         (overlay-eol (make-overlay eol eol))
         (prefix (make-string (- annotate-annotation-column (annotate-line-length)) ? )))
    (overlay-put overlay-highlight 'face annotate-highlight-face)
    (overlay-put overlay-eol 'after-string
                 (concat prefix (propertize
                                 (read-from-minibuffer "Annotation: ")
                                 'face annotate-annotation-face)))))
```
1. 楼主明白 interactive "r" 的含义么？可以用别的字符么？
2. let* 在干什么？ 它和 let 的区别是？假设楼主知道了 let 的意义。

--- 
假设楼主知道了 let 的用法，那么：

1. 为什么要定义4个变量？
或者说，我们在“可视化有色标记文本的时候”，我们对 emacs 下了什么样的命令。

---

这就带出了，Emacs 如何从内存读取 机器码，然后将识别出的文字，呈现在显示器上。

如果把目光聚焦在一个字上。 那么有色标记的意思是什么？

--- 
偷 @casouri 的代码 

```
(defun highlight-region (beg end)
  (interactive "r")
  (overlay-put (make-overlay beg end)
               'face 'highlight))
```

请问 下面两个关键词在 Emacs 中代表什么？

- face
- highlight

```elisp
(defgroup annotate nil
  "Annotate files without changing them."
  :version 0.1
  :group 'text)

(defcustom annotate-file "~/.file-annotations"
  "File where annotations are stored."
  :type 'file
  :group 'annotate)

(defcustom annotate-highlight-face 'highlight
  "Face for annotations."
  :type 'face
  :group 'annotate)

(defcustom annotate-annotation-face 'highlight
  "Face for annotations."
  :type 'face
  :group 'annotate)

(defcustom annotate-annotation-column 90
  "Where annotations appear."
  :type 'number
  :group 'annotate)

(defun annotate-create-annotation (start end &optional arg)
  "Create a new annotation for selected region."
  (interactive "r")
  (let* ((overlay-highlight (make-overlay start end))
         (eol (save-excursion (move-end-of-line nil) (point)))
         (overlay-eol (make-overlay eol eol))
         (prefix (make-string (- annotate-annotation-column (annotate-line-length)) ? )))
    (overlay-put overlay-highlight 'face annotate-highlight-face)
    (overlay-put overlay-eol 'after-string
                 (concat prefix (propertize
                                 (read-from-minibuffer "Annotation: ")
                                 'face annotate-annotation-face)))))

(defun annotate-line-length ()
  "The length of the line from beginning to end."
  (save-excursion
    (move-end-of-line nil)
    (let ((eol (point)))
      (move-beginning-of-line nil)
      (- eol (point)))))
 ```
*** reply by "在核潜艇里工作" de emacs' user?

- interactive 是交互的意思，在函数的body第一句话（不知道在别的什么地方加会怎样）加，就可以在eshell中调用，"r"是个控制符，不能换成别的，类似地还有"n,s"分别表示从屏幕读取两个参数，读取一个数字，读取一个字符串。
- let和let的区别在于(let(这里面定义的临时变量，可以在这里面进行操作))
- 4个变量，前两个是控制选中区域的两个位置，后面两个不知道是ha
- 如果把目光聚焦在一个字上。 那么有色标记的意思是什么？这个是个人理解吗？
- overlay-put,make-overlay 不是很清楚这两个东西的用法，您可以解释一下
