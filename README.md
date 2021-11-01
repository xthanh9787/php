Github Flow và cách làm việc nhóm trên Github
===

# Các loại branch

| Tên branch | Ý nghĩa |Lưu ý|
| :----------|-----|----|
|master |branch gốc dùng để deploy sản phẩm, phải luôn sẵn sàng và giới hạn người thao tác trên branch này |feature,release phải lấy từ master ra |
|feature |branch sử dụng khi develop |mỗi branch sẽ chỉ tập trung vào viết 1 yêu cầu/ tính năng cụ thể |
|release |branch để deploy sản phẩm | |
|hotfix|branch dùng để sửa lỗi khẩn cấp| chỉ dùng khi sản phẩm đã release |

Đây là định nghĩa của Gitflow, các bạn có thể tham khảo cụ thể hơn tại đây [A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/).

Tuy nhiên, lần này chúng ta sẽ sử dụng [Github Flow](https://guides.github.com/introduction/flow/index.html), nên cấu trúc sẽ đơn giản hơn. Trong Github, ta chỉ sử dụng master và feature mà thôi. 

![スクリーンショット 2014-06-13 17.37.11.png](https://qiita-image-store.s3.amazonaws.com/0/14914/944ced26-dd08-b467-19d1-ff05e81f8dc6.png "スクリーンショット 2014-06-13 17.37.11.png")

Giải thích 1 chút về hình trên: 
- Tạm thời không quan tâm tới release branch.
- Ta có 2 đặc tính A, B cần phải làm song song. Đặc tính A đơn giản, nên sẽ có 1 bạn làm. Đặc tính B phức tạp hơn 1 chút nên sẽ có 2 bạn làm. 
- Đầu tiên bạn làm đặc tính A: 
    - tạo 1 branch từ master để làm, bạn ấy sẽ đặt tên là `feature/A`.
    - Bạn ấy sửa và có 3 commit như hình trên.
    - Sau khi làm xong, bạn ấy sẽ gửi 1 pull request để leader sẽ merge branch `feature/A` vào master.
    - Đến đây là xong.
- Tiếp theo là 2 bạn làm đặc tính B:
    - tạo 1 branch từ master để làm, bạn ấy sẽ đặt tên là `feature/B`. Sau khi commit 1 lần, bạn thứ 2 sẽ vào làm tiếp bằng cách tạo thêm 1 branch `feature/B-1` từ branch `feature/B`.
    - 2 bạn này sẽ tiến hành song song, và tại thời điểm thích họp sẽ merge 2 branch `feature/B` và `feature/B-1` lại thành 1.
    - Sau khi xong việc, 2 bạn này gửi pull request để leader sẽ merge branch `feature/A` vào master.

Đến đây ta có 1 số quy ước sau.

# Quy ước

* Việc merge các branch với nhau sẽ được làm trên github thông qua các pull requests. Không merge trên local.

* Việc merge vào master sẽ chỉ do leader tiến hành. TUYỆT ĐỐI không merge trên local rồi tự ý push vào master. merge các branch khác (như `feature/B` và `feature/B-1` như trên) thì không cần leader.

* Sau khi hoàn thành xong các branch (review, fix, ...) sẽ rebase lại các commit rồi push chèn lên remote.

# Thao tác thực tế

## Thao tác cơ bản
Tạo branch

```console
git checkout -b create_test origin/master
```

Sau khi viết xong sẽ push vào remote:

```console
# kiểm tra lại các đoạn code đã sửa
git diff

git add -A
git commit

# kiểm tra xem master có thay đổi hay không
git fetch

# Nếu master có thay đổi thì phải rebase lại để tránh conflict
# Nếu có conflict thì sửa lại, commit lại
git rebase master

# Cuối cùng thì push vào remote
git push origin create_test
```

Vào github và tạo 1 pull request, click vào "Compare & pull request"

![スクリーンショット 2014-06-13 18.33.15.png](https://qiita-image-store.s3.amazonaws.com/0/14914/1b6a3e6a-4f81-cd2b-b4d8-91ffd84303ae.png "スクリーンショット 2014-06-13 18.33.15.png")

Kiểm tra lại những code đã sửa xem có vấn đề gì không. Điền các thông tin vào, chọn người assignee rồi "create pull request"

![スクリーンショット 2014-06-13 18.36.00.png](https://qiita-image-store.s3.amazonaws.com/0/14914/510e9e60-1008-d438-f50d-c889ea348c31.png "スクリーンショット 2014-06-13 18.36.00.png")

Người được assignee có trách nhiệm merge nên phải review code mới. những comments sẽ được viết trực tiếp trên Github, không trao đổi bằng miệng.

![スクリーンショット 2014-06-13 18.38.15.png](https://qiita-image-store.s3.amazonaws.com/0/14914/73bae4d0-5514-ab9a-dcf1-22aafc85bcb4.png "スクリーンショット 2014-06-13 18.38.15.png")

Nếu có comment và phải sửa code thì người đảm nhiệm branch ấy sẽ sửa lại và push lại. Việc này sẽ tiến hành đến khi nào người được assignee chấp nhận thì thôi.

Đến khi không có vấn đề gì nữa, người được assignee sẽ merge và delete branch ấy đi.

## Thao tác khi phải rebase
Khi master thay đổi (đã merge với 1 feature khác, ...) và feature hiện tại không thể merge được, github sẽ thông báo như sau.
`We can’t automatically merge this pull request`
Có 2 cách xử lý, cách thứ nhất là merge bằng tay trên local rồi push lên master, nhưng cách này vi phạm quy ước ở trên nên sẽ không dùng. Cách 2 là rebase lại master.
**Lưu ý** phân biệt rõ ràng sự khác và giống nhau giữa merge và rebase.

Dưới đây là thao tác khi rebase:
```console
# quay về master
git checkout master

# cập nhật master
git pull origin master

# quay về branch đang làm việc
git checkout branch_name

# rebase lại với master
git rebase master

# Nếu có conflict thì sửa lại

# thêm những đoạn code đã sửa lại vào feature
# đến đây thì KHÔNG ĐƯỢC commit
git add .

# tiếp tục rebase
git rebase --continue

# push lại lên remote
# ở đây có thêm tham số -f để ghi đè lên remote
git push -f origin branch_name
```

