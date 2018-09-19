===============================================================================
ime.vim
===============================================================================

Read this document in `English <README.en.rst>`_.

介紹
-------------------------------------------------------------------------------
在 Vim 裡面輸入中文一直都是件麻煩事。

有在使用中文輸入法的人都會知道，每個中文輸入法都有兩種狀態：

* 英文
* 中文

Vim 也有兩種狀態：

* Insert mode (以及 replace 和類似的狀態)
* 非 insert mode (例如 normal mode 和 command mode 等等)

這些狀態在使用時會疊在一起，如下表：

+----------------+------+------+
| Vim \ 輸入法   | 英文 | 中文 |
+----------------+------+------+
| Insert Mode    | :)   | :)   |
+----------------+------+------+
| 非 Insert Mode | :)   | :(   |
+----------------+------+------+

這四種狀況中，中文 + 非 insert mode 非常討厭，按下的按鍵是中文的字根，
會被輸入法攔截下來，不會直接進入 Vim。

如果能把這個狀況去除，就可以避免
「需要不斷的按下 ``shift`` 或是 ``ctrl`` + ``space`` 」
（在 macOS 上也許是 ``⌘`` + ``space`` ）的狀況。
要達到這個目標，最好的方式就是在 Vim 中嵌入一個中文輸入法。

ime.vim 透過純 VimScript 實作了一個嘸蝦米輸入法引擎，
不需要外部程式，不需要網路，
安裝以後就能馬上開始使用。


安裝
-------------------------------------------------------------------------------
ime.vim 可以使用
`Vundle <https://github.com/gmarik/Vundle.vim>`_
或是 `vim-plug <https://github.com/junegunn/vim-plug>`_
安裝，請參考它們的安裝教學。

在某些環境下，你可能沒有辦法正常使用 plugin manager，
此時仍然可以手動安裝，請參考 `手動安裝 ime.vim <README-manually-install.rst>`_

ime.vim 在以下環境測試過：

* Vim 7.4 / Mac OS X
* Vim 8.0 / macOS Sierra
* Vim 7.4 / FreeBSD
* Vim 8.0 / FreeBSD
* gVim 7.3 / Windows 7


相關前作
-------------------------------------------------------------------------------
個人是嘸蝦米的使用者，但目前能力不足，不便購買行易公司的嘸蝦米輸入法，
所以先尋找前人的作品。

* `VimIM <http://www.vim.org/scripts/script.php?script_id=2506>`_

  - 據說很強大的中文輸入法
  - 支援相當多的中文輸入法 (包含數種雲端輸入法)

* `boshiamy-cue <http://www.vim.org/scripts/script.php?script_id=4392>`_

  - 感覺很輕量，但很老舊的 Plugin

* `vim-boshiamy <https://github.com/dm4/vim-boshiamy>`_

  - 從 VimIM 0.9.9.9.6 fork 出來，並為嘸蝦米做了調整

VimIM 的功能非常強大，但個人覺得強大的軟體就會很肥大，所以沒有嘗試。
我希望找到一個剛好符合需求，不要有太多彩蛋或不必要功能的軟體。

boshiamy-cue 則是年代久遠，在 2013 年初發佈第一個版本後就沒有再更新，
也因此這個 Plugin 並沒有考慮 Pathogen/Vundle。
此外嘸蝦米還是需要選字的，而 boshiamy-cue 沒有提供這個功能。

vim-boshiamy 是暫時的 work around，2012 年中釋出後就沒有再更新。


使用
-------------------------------------------------------------------------------
* ``ime#mode()`` 函式回傳輸入法當前的狀態，你可以在自己的 ``statusline`` 中顯示這個資訊： ::

    set statusline=%<%{ime#mode()}%f\ %h%m%r%=%-14.(%l,%c%V%)\ %P

  - 這行 ``statusline`` 看起來會像 ``[嘸]README.rst [+]      75,67-59  53%``
  - 在 Vim 7.4.1711 之前，直接把它放在 ``statusline`` 裡面可能會導致 ``statusline``
    被重設。包裝一層函式可以暫時解決這個問題： ::

      function! IMEStatusString ()
          if exists('*ime#mode')
              return ime#mode()
          endif
          return ''
      endfunction
      set statusline=%{IMEStatusString()}

* 切換輸入法/英文： ::

    let g:ime_toggle_english = ',,'

  - 在英文與目前選定的輸入模式之間切換
  - 如果目前的模式是日文，用 ``,,`` 切換回英文再切換回來仍會是日文

* 選擇主要輸入模式： ::

    let g:ime_select_mode = ',m'

  - 設定 ``g:ime_select_mode_style`` 以使用不同的風格：

    + ``let g:ime_select_mode_style = 'popup'`` - (預設) 透過補完選單切換模式
    + ``let g:ime_select_mode_style = 'interactive'`` - 透過互動式選單切換模式

* 交換主要/次要輸入模式： ::

    let g:ime_toggle_2nd = ',.'

  - 若不想在 statusline 顯示兩個輸入模式： ::

      let g:ime_show_2nd_mode = 0

* 取消輸入，回復為輸入前的字串： ::

    let g:ime_cancel_input = '<C-h>'

  - 有些英文單字如 ``are`` 是某些字的字根，如果開著中文模式輸入英文，會讓這些英文單字變成中文，此時按下 ``<C-h>`` 就可以把字打回英文，並在後方加上一個空白字元

* 切換到子模式： ::

    let g:ime_switch_submode = ';;'

  - 有些輸入模式有多個子模式
  - 就像微軟新注音輸入法，按下 ``ctrl`` + ``alt`` + ``,`` 以後會跳出螢幕小鍵盤，此時按下的按鍵會輸出標點符號而不是注音符號
  - 不一定每個模式都有子模式，它們的按鍵也不一定相同，請參考它們的說明文件

* 內建輸入模式

  - 中文 ``[嘸]``

    + 可直接輸入嘸蝦米，按下空白鍵送字
    + 輸入 ``;`` 後可直接以注音輸入（有些字真的臨時忘了怎麼寫）

      * 輸入 ``;hk4`` ，按下空白鍵送字以後會跳出 ``測`` 的同音字選單

    + 輸入 ``\u`` 後可使用 Unicode Code Point 輸入 Unicode 字元
    + ``\u[字]`` 可把 ``字`` 解碼為 ``\u5b57``

  - 日文假名 ``[あ]`` / ``[ア]``

    + ``;;`` 可在平假名與片假名之間切換
    + 按下 ``v`` 可把前一個假名改為促音
    + 平假名範例

      * ``a`` -> ``あ``
      * ``あv`` -> ``ぁ``
      * ``buiaiemu`` -> ``ぶいあいえむ``

* 自訂字根表

  - 使用者可以自訂字根表，這個字根表的優先度比內建的表格高，使用者可以用來新增甚至修改組字規則
  - 自訂字根表的檔名： ::

      let g:ime_boshiamy_custom_table = '~/.boshiamy.table'

    + 此全城變數 *沒有* 預設值，請在需要使用時再設定

  - 自訂字根表格式為 ``字串 字根 字根 ...`` ，中間以空白字元分隔： ::

      (((°Д°;))  ,face
      (ಥ_ಥ)      ,face
      ಠ_ಠ        ,face ,stare
      ఠ_ఠ        ,face ,stare
      (ゝω・)    ,face
      (〃∀〃)    ,face
      (¦3[▓▓]    ,face ,sleep
      (눈‸눈)    ,face
      ㅍ_ㅍ      ,face

    + 先後順序和選字選單的順序相同

* 載入第三方套件（後述）::

    let g:ime_plugins = ['emoji', 'runes']

* 啟用 ime buffer ::

    let g:ime_enable_ime_buffer = 1

  - ime buffer 的效果為

    + 在 Insert mode 按下 Enter 會直接剪下並複製一行
    + 在 Visual mode 按下 Enter 會剪下並複製多行
    + 若該行是空的，Enter 會貼上先前複製的文字

  - 透過指定 filetype 來啟動 ime buffer ::

      :set ft=ime

詳細的文件請參考 ``:help ime.vim``


對嘸蝦米字表的改動
-------------------------------------------------------------------------------
為了方便，我自己更改了嘸蝦米的字表，新增/刪除了一些項目，此處不細述，只大概列出一些比較重要的改動。

* 全型格線的輸入都使用 ``,g`` 開頭，接上形狀： ``t`` / ``l`` / ``i`` / ``c``

  - ``,gt`` -> ``┬`` （其他方向的符號在選單中會列出）
  - ``,gl`` -> ``┌``
  - ``,gi`` -> ``─``
  - ``,gc`` -> ``╭``
  - 重覆形狀可以輸入雙線的格線符號，最多三次

    + ``,gttt`` -> ``╦``

* 嘸蝦米模式中的日文片假名、平假名被我刪除，否則 ``u，`` 會無法正常輸入
* 新增 Mac OS X 相關的特殊符號

  - ``,cmd`` / ``,command`` -> ``⌘``
  - ``,shift`` -> ``⇧``
  - ``,option`` / ``,alt`` -> ``⌥``


第三方套件
-------------------------------------------------------------------------------
ime.vim 能夠載入第三方套件，以擴充自己的輸入能力。

目前已經有的套件有：

* `ime-emoji.vim <https://github.com/pi314/ime-emoji.vim>`_ - 輸入 emoji 符號
* `ime-runes.vim <https://github.com/pi314/ime-runes.vim>`_ - 輸入盧恩字母
* `ime-wide.vim <https://github.com/pi314/ime-wide.vim>`_ - 輸入全型字
* `ime-braille.vim <https://github.com/pi314/ime-braille.vim>`_ - 輸入點字
* `ime-phonetic.vim <https://github.com/pi314/ime-phonetic.vim>`_ - 注音輸入法

這些套件原本都是 ime.vim 的一部份，現在拔出核心，更加彈性。

要注意 ime.vim 本身並不管理套件，請手動安裝，或是透過
`Vundle <https://github.com/gmarik/Vundle.vim>`_ 、
`vim-plug <https://github.com/junegunn/vim-plug>`_ 等套件管理系統安裝。

第三方套件的開發請參考 ``:help ime-plugins``
或是 ``doc/ime-plugins.txt``


可以配合 vim 使用的技巧
-------------------------------------------------------------------------------
在取代模式中，一個字元只會覆蓋一個字元，無論寬度。

在繪製 ASCII 圖片時，如果用中文字去覆蓋空白字元，會讓那行變得越來越長，
因為一個兩格寬的中文字卻只覆蓋了一個空白字元。

此時 vim 內建的 ``gR`` 變得很有用，它可以根據字元的寬度覆蓋字元。


特別感謝
-------------------------------------------------------------------------------
* honglong0420 - 在 twitter 上提到 ``𡦀`` 這個字
* <anonymous> - 在 twitter 上提到 ``龖`` 和 ``厵`` 這兩個字


授權
-------------------------------------------------------------------------------
本軟體使用 2-clause BSD license 發佈，請參考 LICENSE.txt

--------

2017/03/30 pi314 @ HsinChu
