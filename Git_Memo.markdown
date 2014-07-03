### git reset
* git resest HEAD^
	
	將最後一個的 commit 撤除，且不保留在 stage 區(也就是回到沒做 git add 前)；但保留修改的內容。
	
* git reset HEAD^ --soft

    將最後一個的 commit 撤除，且保留在 stage 區(也就是留在 git add)；但保留修改的內容。
    
* git reset HEAD^ --hard
    
    將最後一個的 commit 撤除，回到前一個 commit 狀態。不保留修改內容。切記要使用前，多考慮一下。
