https://stackoverflow.com/questions/102384/using-vims-tabs-like-buffers

:help tabpage.txt

^^^
Tabs are also a nice way to edit a buffer temporarily without changing the
current window layout.  Open a new tab page, do whatever you want to do and
close the tab page.

Открытие новых вкладок с помощью модификатора :tab <команда> ->
	* :tab help gt      " Открывает новую вкладку с помощью для команды gt
	* :tab split        " Открывает текущий буфер в новой вкладке

{count}gt	Go to tab page {count}.  The first tab page has number one.
gT - goto previous tab

Вопросы:

1) Как переименовывать табы в NVIM?
2) Как номер вкладки установить, чтобы через {N}gt можно было быстро ходить? кол-во окон на вкладке - бесполезная информация



