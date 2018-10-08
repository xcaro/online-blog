---
title: "Mẹo vặt rsync"
category: blog
layout: post
tag:
  - Linux
  - tips
---

`rsync` là lệnh Linux (Mac cũng có) hay được dùng để đồng bộ (sync) 2 thư mục trên cùng một máy hoặc khác máy. Tất nhiên có thể dùng những công cụ khác như `git` hoặc `svn` để làm việc này, nhưng với trường hợp cần copy đơn giản thì không cần dùng dao mổ trâu để mổ gà như vậy, dùng `rsync` nhẹ nhàng hơn vì cảm giác dùng nó giống như dùng lệnh cp.

Mình sẽ giới thiệu vài use case đơn giản:
![rsync](/assets/images/rsync.svg)*rsync*

### Option: *-av --delete*
Giả sử, nếu bạn muốn copy thư mục a thành thư mục b, dĩ nhiên ta sẽ dùng lệnh cp:
```
cp -r a b
```

Sau đó giả sử mình sửa vài tập tin trong thư mục a, rồi muốn đồng bộ hóa sang thư mục b. Nếu nhớ rõ đã sửa tập tin nào, dĩ nhiên chỉ cần copy chúng sang b. Nhưng không nhớ thì sao? Cách trâu bò dĩ nhiên là xoá b đi rồi copy lại.

Nhưng nếu thư mục a lớn, chứa rất nhiều tập tin (vài chục gàn chẳng hạn), thời gian copy sẽ lâu. Nếu chỉ phải làm một lần thì cũng chấp nhận được. Nhưng nếu một ngày phải làm chục lần thì rất phiền. Lúc này sẽ dùng `rsync`:

```
rsync -av --delete a/ b
```

*Giải thích:*
* `-a` để bảo rsync hãy giữ nguyên attribute của các tập tin
* `-v` là verbose
* `–delete` là tập tin nào có trong b mà không có trong a (ta đã xoá chẳng hạn), thì nó sẽ bị xóa
* Về dấu / ở sau a, hãy thử nghiệm 2 trường hợp có và không có dấu này sẽ hiểu.

### Option: *--exclude*
Nếu mấy bạn muốn `rsync` ignore thư mục hoặc tập tin nào đó, thì như này:
```
rsync -avz --delete --exclude=.* --exclude=*.log a/ b
```
*Có 2 ý:*
* Nếu a chứa những tập tin (và thư mục) như vậy, chúng sẽ không được copy sang b.
* Nếu b chứa những thứ như vậy, chúng sẽ không bị xóa (và ghi đè v.v.).

### Option: *-z -e ssh*
Nếu mấy bạn muốn copy thư mục a sang server khác thông qua SSH, dĩ nhiên đơn giản nhất sẽ là dùng lệnh scp. Nhưng nếu muốn `sync` thì nên dùng `rsync`:
```
rsync -avz --delete -e ssh a/ user@server:/path/to/b
```
*Giải thích:*
* `-z` để bảo rsync hãy vừa nén vừa copy, khi truyền dữ liệu qua mạng thì nói chung nén lại sẽ nhanh hơn
* Nếu SSH dùng port không phải 22 mà là xxx, thì dùng `-e "ssh -p xxxx"`; nếu đã cấu hình để login SSH mà không bị hỏi `password`, thì dĩ nhiên không cần tham số `-e` này.
<br/>

Trên đây là hướng dẫn đơn giản nhằm khai phá cho những bạn chưa dùng `rsync` bao giờ.  `rsync` còn nhiều cách dùng phức tạp hơn, như chạy trong chế độ daemon, tạo incremental (continuous) backup, etc. Muốn tìm hiểu thêm thì ta cùng search Google.