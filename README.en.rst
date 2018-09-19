===============================================================================
ime.vim
===============================================================================

`這份文件的中文版。 <README.rst>`_

Introduction
-------------------------------------------------------------------------------
It's always a trouble to input Chinese characters in Vim.

Everyone who uses Chinese Input Method knows that they have two states:

- English mode
- Chinese mode

Vim has two states, too:

- Insert mode (and similar modes)
- Non-insert mode (like normal mode or command mode)

So we have four combinations:

+--------------------+---------+---------+
| Vim \ Input method | English | Chinese |
+--------------------+---------+---------+
| Insert mode        | :)      | :)      |
+--------------------+---------+---------+
| Non-insert mode    | :)      | :(      |
+--------------------+---------+---------+

The "Chinese / Non-insert mode" is very troublesome,
your keyboard input will be intercepted
by the input method and will not go into Vim.

If we can get rid of this situation, we can avoid keep hitting
``shift`` or ``ctrl`` + ``space`` to close input method
(``⌘`` + ``space`` on macOS).
And the best way to do this is to embedded an input method in Vim.

ime.vim implements a Input Method Engine in pure VimScript.
Not depends on external programs, not depends on network.
After installing it, you can use it right the way.


Installation
-------------------------------------------------------------------------------
ime.vim can be installed with
`Vundle <https://github.com/gmarik/Vundle.vim>`_
or `vim-plug <https://github.com/junegunn/vim-plug>`_.
Please refer to their installation tutorial

Under some environment, plugin managers may not operate normally
(e.g. Windows 7 without any of ``git``, ``curl``, ``fetch``, ``wget`` installed).
You can still `install it manually <README-manually-install.en.rst>`_.

ime.vim is tested under these environments:

* Vim 7.4 / Mac OS X
* Vim 8.0 / macOS Sierra
* Vim 7.4 / FreeBSD
* Vim 8.0 / FreeBSD
* gVim 7.3 / Windows 7


Related works
-------------------------------------------------------------------------------
I'm a 嘸蝦米 input method user, but currently I'm unable to buy one,
so I do a little survey first:

* `VimIM <http://www.vim.org/scripts/script.php?script_id=2506>`_

  - It's said that it is very powerful
  - It supports many kind of Chinese input methods (including cloud input methods)

* `boshiamy-cue <http://www.vim.org/scripts/script.php?script_id=4392>`_

  - Light weight, but old

* `vim-boshiamy <https://github.com/dm4/vim-boshiamy>`_

  - Fork from VimIM 0.9.9.9.6 and adjusted for 嘸蝦米

VimIM is powerful, but I have a biased view that "powerful software is fat",
so I haven't try it.
I want a software that just fit my need.

boshiamy-cue is very old, and it doesn't update since first version in early 2013.
This plugin doesn't consider Pathogen and Vundle.
Besides, 嘸蝦米 still needs word-choosing,
which is a feature that boshiamy-cue unable to provide.

vim-boshiamy is a temporary workaround, it doesn't update since middle 2012.


Usage
-------------------------------------------------------------------------------
* ``ime#mode()`` returns the current status of ime.vim, you can show this
  information in your ``statusline`` ::

    set statusline=%<%{ime#mode()}%f\ %h%m%r%=%-14.(%l,%c%V%)\ %P

  - This ``statusline`` looks like ``[嘸]README.rst [+]      75,67-59  53%``
  - Before Vim 7.4.1711, directly put it into ``statusline`` may cause ``statusline``
    being reset. Wrap it with another function solves this problem ::

      function! IMEStatusString ()
          if exists('*ime#mode')
              return ime#mode()
          endif
          return ''
      endfunction
      set statusline=%{IMEStatusString()}

* Toggle English ::

    let g:ime_toggle_english = ',,'

  - The previous mode will be kept after switch to English mode.

* Select mandatory input mode ::

    let g:ime_select_mode = ',m'

  - Set ``g:ime_select_mode_style`` for different selection style:

    + ``let g:ime_select_mode_style = 'popup'`` - (default) use completion menu for selection
    + ``let g:ime_select_mode_style = 'interactive'`` - use interactive menu for selection

* Switch mandatory/secondary input modes ::

    let g:ime_toggle_2nd = ',.'

  - If you don't want to show both modes on statusline ::

      let g:ime_show_2nd_mode = 0

* Cancel input text ::

    let g:ime_cancel_input = '<C-h>'

  - Some English words (such as ``are``) are the roots of some Chinese characters.
    If you type English in Chinese mode, these words become Chinese characters.
    You can press ``<C-h>`` to turn them back to English.

* Switch to submode ::

    let g:ime_switch_submode = ';;'

  - Some input mode has several submodes
  - Like Microsoft phonetic input method, after pressing ``ctrl`` + ``alt`` + ``,``, a screen keyboard pops up, and the next key will output a punctuation mark instead of phonetic symbol
  - Not every input mode has submodes, and their switch keys would not be the same, please refer to their documents

* Built-in input modes

  - Built-in input modes are triggered with ``<space>``
  - 嘸蝦米 input mode ``[嘸]``

    + Type ``;`` and preceeding with phonetic roots
      (in case you forgot how to write a Chinese character)

      * Type ``;hk4``, press ``<space>``,
        all characters with same sound with ``測`` will pop up

    + Type ``\u`` and proceeding with unicode code point
    + ``\u[字]`` decodes ``字`` to ``\u5b57``

  - Kana mode ``[あ]`` / ``[ア]``

    + Type ``;;`` to switch between Japanese Hiragana and Katakana
    + Type ``v`` to change previous symbol to "sokuon"
    + Examples:

      * ``a`` -> ``あ``
      * ``あv`` -> ``ぁ``
      * ``buiaiemu`` -> ``ぶいあいえむ``

* Custom table

  - You can customize your input table.
    This table's priority is higher than the built-in one.
  - Custom table filename ::

      let g:boshiamy_custom_table = '~/.boshiamy.table'

    + This global variable has *no* default value

  - The format of custom table is ``string root root ...``, separate with a space ::

      (((°Д°;))  ,face
      (ಥ_ಥ)      ,face
      ಠ_ಠ        ,face ,stare
      ఠ_ఠ        ,face ,stare
      (ゝω・)    ,face
      (〃∀〃)    ,face
      (¦3[▓▓]    ,face ,sleep
      (눈‸눈)    ,face
      ㅍ_ㅍ      ,face

    + The ordering in this table will be kept

* Load third party plugins ::

    let g:ime_plugins = ['emoji', 'runes']

* Enable ime buffer ::

    let g:ime_enable_ime_buffer = 1

  - In ime buffer

    + Copy-and-cut a line by pressing Enter in Insert mode
    + Copy-and-cut multiple lines by pressing Enter in visual mode
    + If the line is empty, pressing Enter pastes it back

  - To activate ime buffer, set filetype to ``ime`` ::

      :set ft=ime

For further information please refer to ``:help ime.vim.en``


Changes to 嘸蝦米 table
-------------------------------------------------------------------------------
* Box-drawing characters start with ``,g``, and proceed with their shape: ``t`` / ``l`` / ``i`` / ``c``

  - ``,gt`` -> ``┬``
  - ``,gl`` -> ``┌``
  - ``,gi`` -> ``─``
  - ``,gc`` -> ``╭``
  - Repeat the shape to generate double-lined symbol, up to three times:

    + ``,gttt`` -> ``╦``

* Katakana and Hiragana are removed in 嘸蝦米 table in order to allow ``u，`` to be typed
* macOS related symbols are added:

  - ``,cmd`` / ``,command`` -> ``⌘``
  - ``,shift`` -> ``⇧``
  - ``,option`` / ``,alt`` -> ``⌥``


Third party plugins
-------------------------------------------------------------------------------
ime.vim is able to load third parth plugins.

Currently these plugins are available:

* `ime-emoji.vim <https://github.com/pi314/ime-emoji.vim>`_ - Emoji
* `ime-runes.vim <https://github.com/pi314/ime-runes.vim>`_ - Runes
* `ime-wide.vim <https://github.com/pi314/ime-wide.vim>`_ - Wide characters
* `ime-braille.vim <https://github.com/pi314/ime-braille.vim>`_ - Braille
* `ime-phonetic.vim <https://github.com/pi314/ime-phonetic.vim>`_ - Phonetic input method

All these plugins are part of ime.vim, now they are pluggable.

Note that ime.vim doesn't manage these plugins.
Please install them manually or through `Vundle <https://github.com/gmarik/Vundle.vim>`_ or
`vim-plug <https://github.com/junegunn/vim-plug>`_.

Further information about developing third party plugins for ime.vim:
``:help ime-plugins.en``


Tips
-------------------------------------------------------------------------------
In replace mode, one character override one character,
no matter the width.

When drawing ASCII graphs, if you use Chinese characters to replace
space characters, the line under cursor will be longer and longer,
because a two-width Chinese character only replaces one space character.

The vim built-in ``gR`` is very useful in this case.


Thanks
-------------------------------------------------------------------------------
honglong0420 - for mentioning ``𡦀`` on twitter



License
-------------------------------------------------------------------------------
This software is released under 2-clause BSD license, please refer to LICENSE.txt.

--------

2017/03/30 pi314 @ HsinChu
