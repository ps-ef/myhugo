+++
author = "Tan Linh"

date = 2018-03-14T22:20:08+07:00
description = "Mình đã chuyển từ Neovim sang Spacemacs như thế nào"
draft = false
slug = "moving-from-neovim-to-spacemacs"
tag = ["vim", "neovim", "spacemacs"]
title = "Moving from Neovim to Spacemacs"
+++

Một năm trước, mình nghe theo lời đường mật [Di cư sang Vim](https://trietphm.github.io/post/sublime-text-to-vim/) của CTO. Sau một năm xài Vim (thực ra là Neovim) một cách nghiêm túc, lần này mình lại bị dụ dỗ sang Spacemacs. Với những ai chưa biết, Spacemacs là một distribution của Emacs với description là `The best editor is neither Emacs nor Vim, it's Emacs and Vim!` cho bạn một trải nghiệm Emacs rất tuyệt vời, riêng Vimmer sẽ cảm giác thân thuộc như ở nhà. Bởi vậy, bài viết này nhằm lôi kéo anh em Vimmer sang Spacemacs kẻo mình cô đơn quá :adore:

# 1. First impression
Sau một tuần sử dụng Spacemacs, mình thấy một số điểm như sau:

- UI đẹp, nuột, rất giống `hắc cơ`
- Cảm giác nhanh mà nuột hơn Neovim. Trừ việc startup time cực lâu nếu so với vim thì còn lại là tuyệt vời. Nếu muốn giảm thời gian startup time, bạn có thể tham khảo bài viết [Chạy spacemacs/emacs với daemon mode](https://kipalog.com/posts/Chay-spacemacs-emacs-voi-daemon-mode) của lão Huytd 
- Toàn bộ command sẽ bắt đầu bằng `leader key`. Mặc định là `SPC` (spacebar) khi sử dụng Vim style hoặc hybrid style, `M-m` khi sử dụng emacs style. Mình thấy điểm này khá hay khi toàn bộ command bắt nguồn từ một phím cố định, nếu bạn nhỡ quên một chút thôi thì đã có `Spacemacs root menu` gợi ý với giao diện cực kỳ trực quan. Bạn có thể xem ở screenshot bên dưới.

![Spacemacs root](https://i.imgur.com/cLhYhlw.png)

- Custom Spacemacs đòi hỏi bạn phải vận động tay chân nhiều hơn so với Vim. Tuy nhiên khi cài xong Spacemacs và enable một số layers là mình có thể bơi vào code luôn được rồi. Spacemacs có sẵn `go` layer cho anh em Gopher luôn.

# 2. Installation

Cài đặt Spacemacs khá đơn giản, bạn cần Emacs version 24.4 hoặc cao hơn, sau đó clone repository của Spacemacs về là xong. 

```
git clone https://github.com/syl20bnr/spacemacs ~/.emacs.d
```

Mở emacs từ terminal và chờ Spacemacs hoàn thành phần cài đặt là bạn có thể bắt đầu trải nghiệm rồi. Tada….

![Screenshot](https://i.imgur.com/8Vly4kq.png)

# 3. Migrate from Vim

Về cơ bản, những concept như `mode`, `buffer`, `plugin` .v.v. bên Vim có thì Spacemacs hay nói đúng hơn là Emacs cũng có nhưng với một tên gọi khác. Anh em Vimmer có thể đọc ở bài viết [Migrating from Vim](http://spacemacs.org/doc/VIMUSERS.html) để hiểu rõ hơn. 

Để bắt đầu cấu hình Spacemacs, bạn có thể dùng Vim edit file `~/.spacemacs` :troll:. Mình đùa thôi, bạn có thể sửa file `.spacemacs` ngay trong Spacemacs bằng tổ hợp phím `SPC f e d` và apply modifications bằng tổ hợp `SPC f e R`. Vô cùng tiện lợi.

Layers trong Spacemacs tương đương với `Plugin` trong Vim. Bạn dễ dàng enable/disable layers bằng cách comment hoặc uncomment tên layer trong block `dotspacemacs-configuration-layers`. Ví dụ đây là layer mình đang sử dụng:

```
dotspacemacs-configuration-layers
   '(
     go
     gtags
     themes-megapack
     imenu-list
     ;; ----------------------------------------------------------------
     ;; Example of useful layers you may want to use right away.
     ;; Uncomment some layer names and press <SPC f e R> (Vim style) or
     ;; <M-m f e R> (Emacs style) to install them.
     ;; ----------------------------------------------------------------
     ;; ivy
     auto-completion
     better-defaults
     emacs-lisp
     git
     markdown
     ;; org
     ;; (shell :variables
     ;;        shell-default-height 30
     ;;        shell-default-position 'bottom)
     ;; spell-checking
     syntax-checking
     ;; version-control
     osx
     )
```

Hiện tại, Spacemacs có hơn 150 layers chia làm 20 group. Với số lượng layer lớn như vậy, mình tin là thỏa mãn đại đa số nhu cầu của mọi người. Danh sách layer các bạn xem tại [đây](http://spacemacs.org/layers/LAYERS.html). Muốn cài đặt layer nào, bạn chỉ việc thêm nó vào block `dotspacemacs-configuration-layers` và apply để cài đặt là xong. Cực kỳ nhanh gọn phải khôngfo nào

# 4. Usage  
 
Nếu bạn không cấu hình linh tinh, mặc định Spacemacs có editing style là `vim`. Điều này có nghĩa là gì? Anh em Vimmer sẽ thấy thoải mái như ở nhà. Di chuyển, select, copy, paste, undo .. vân vân và vân vân dễ như ăn kẹo. 

[![https://gyazo.com/820e09e1d46e8b4a5fc8042cb3a6e7ab](https://i.gyazo.com/820e09e1d46e8b4a5fc8042cb3a6e7ab.gif)](https://gyazo.com/820e09e1d46e8b4a5fc8042cb3a6e7ab)

Nếu bạn không phải là Vimmer hoặc lần đầu tiên sử dụng Spacemacs? Không sao cả, hãy đọc bài viết sau: [Làm quen với Vim trong 5 phút](https://kipalog.com/posts/Lam-quen-VIM-trong-5-phut). 

Khi đã làm quen với các thao tác cơ bản trong bài viết trên, chúng ta cùng tìm hiểu về `Helm` và `Projectile`. Bạn chắc chắn sẽ dành phần lớn thời gian làm việc với chúng đấy.

### a. Helm layer

`Helm` là một layer tiêu chuẩn mặc định của Spacemacs, vì vậy bạn không cần động tay chân thêm một giây nào nữa để cài đặt nó. Người anh em với `Helm` là `Ivy` nhưng thú thật mình chưa biết nó ngang dọc như nào nên cũng chẳng nói thêm gì được. Nói một cách chính xác, `Helm` là một `completion framework` và nếu dùng một mỹ từ dành cho `Helm` thì nó là bộ não của Spacemacs. Helm được dùng để manage buffer, projects, search results, configuration layers và hàng tá công việc khác... Đầy đủ và chi tiết nhất, các bạn đọc phần [Helm layer](http://develop.spacemacs.org/layers/+completion/helm/README.html)

Ví dụ về vụ tìm kiếm file siêu nhanh, cho phép tìm kiếm đồng thời cả buffer và file. 

[![https://gyazo.com/71d263102b40f3a956fcaff1eb7c5afc](https://i.gyazo.com/71d263102b40f3a956fcaff1eb7c5afc.gif)](https://gyazo.com/71d263102b40f3a956fcaff1eb7c5afc)

### b. Projectile

Projectile là thư viện của Emacs hỗ trợ làm việc với project.  Goal của Projectile là `nice set of features operating on a project level without introducing external dependencies`. Vì vậy, bạn không thấy sự xuất hiện của những thư mục cấu hình theo dạng `.vscode` hoặc `.idea` như Visual Code hay IntelliJ. Bạn không còn thi thoảng phải chửi thề: `Douma quên ignore folder .vscode rồi!!!`. Đối với Projectile, những thư mục chứa những tập tin đặc biệt chính là project. `Git`, `Mercurial`, `darcs` hay `bazaar` sẽ được để ý đầu tiên. Bạn cũng có thể tạo một file rỗng `.projectile` trong thư mục nếu muốn Projectile hiểu đó là một project. 

# 5. Helpful hotkeys

Như mình đã nói ở trên, tất cả command của Spacemacs đều bắt đầu bằng `SPC`. Ngoài ra, key binding của Spacemacs cực kỳ dễ nhớ, ví dụ như `b` cho buffer, `p` cho project, `s` cho tìm kiếm .v.v. Tổng hòa hai điều trên, bạn mất chưa tới 30 phút để nhớ làm việc thoải mái với Spacemacs. Trường hợp quên đi nữa, bạn gõ `SPC` bật Spacemacs root để học thuộc lòng lại từ đầu :troll:


Mình xin phép liệt kê một số hotkeys mà mình thấy hữu ích:

- `SPC s c` : Clear search highlight
- `SPC f t` : Toggle Nerdtree
- `SPC b i` : Toggle imenu-list (Chỉ khi bạn đã install và enable layer `imenu-list`. Với những bạn chưa biết thì `imenu` tương tự như `tagbar` bên Vim nhưng không xịn bằng)
- `SPC T s` : Select theme (Chỉ khi bạn đã install và enable layer `themes-megapack`)
- `SPC f e d` : Modify `.spacemacs` file
- `SPC f e R` : Apply modifications
- `SPC 1` : Switch to window 1 (Riêng khi bạn mở Nerdtree thì nó là window `0`)
- `SPC 2` : Switch to window 2
- `SPC b b` : Show list buffers
- `SPC b d` : Kill current buffer
- `SPC TAB` : Swith to previous buffer
- `SPC p p` : Open project
- `SPC p f` : Find file or buffer
- `SPC w /` : Split window vertically
- `SPC w -` : Split window horizontally
- `SPC w d` : Delete active window
- `SPC m g g` : Go to definition

Biển trời hotkey biết đâu là bờ, mình sẽ liên tục cập nhật list hotkeys để các bạn theo dõi. Với những bạn muốn trải nghiệm Vim, có thể tham khảo [dotfiles](https://github.com/tanlinhnd/dotfiles) của mình. 

Cuối cùng, bài viết này chỉ duy nhất một mục đích là lôi kéo và rủ rê, mặc dù sau khi đọc lại bài viết mình chẳng thấy nó có gì bổ ích. Anh em quan tâm có thể comment bên dưới nhé. Rất mong nhận được góp ý từ anh em.

Many thanks,

P/s: Hi vọng năm sau không có bài `Di cư từ Spacemacs sang Vim` nữa :troll:
