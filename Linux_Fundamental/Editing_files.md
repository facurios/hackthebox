# Editing Files
**NANO Editor**
```
facuuni8@htb[/htb]$ nano notes.txt
```
```
GNU nano 2.9.3                                    notes.txt                                              

Here we can type everything we want and make our notes.▓


^G Get Help    ^O Write Out   ^W Where Is    ^K Cut Text    ^J Justify     ^C Cur Pos     M-U Undo
^X Exit        ^R Read File   ^\ Replace     ^U Uncut Text  ^T To Spell    ^_ Go To Line  M-E Redo
```
Con CTR + W podemos realizar busquedas dentro del texto del archivo
Con CTRL + O podemos guardar y dar nombre al archivo.

Con el comando **cat** podemos visualizar en el shell el contenido del archivo

**VIM EDITOR**
Mode	Description
Normal	In normal mode, all inputs are considered as editor commands. So there is no insertion of the entered
        characters into the editor buffer, as is the case with most other editors. After starting the editor, we are usually in the normal mode.
Insert	With a few exceptions, all entered characters are inserted into the buffer.
Visual	The visual mode is used to mark a contiguous part of the text, which will be visually highlighted. By
        positioning the cursor, we change the selected area. The highlighted area can then be edited in various ways, such as   deleting, copying, or replacing it.
Command	It allows us to enter single-line commands at the bottom of the editor. This can be used for sorting, replacing
        text sections, or deleting them, for example.
Replace	In replace mode, the newly entered text will overwrite existing text characters unless there are no more old
        characters at the current cursor position. Then the newly entered text will be added.
Ex	    Emulates the behavior of the text edtior Ex, one of the predecessors of Vim. Provides a mode where we can
        executemultiple commands sequentially without returning to Normal mode after each command.

When we have the Vim editor open, we can go into command mode by typing ":" and then typing "q" to close Vim.