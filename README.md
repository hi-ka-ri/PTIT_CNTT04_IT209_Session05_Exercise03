\# Bài 3: Xử lý xung đột phức tạp trong quá trình Rebase



\## 1. Mục tiêu



Bài thực hành giúp hiểu cách xử lý xung đột khi sử dụng `git rebase`.



Các mục tiêu chính:



\- Hiểu sự khác nhau giữa conflict trong Merge và Rebase.

\- Xử lý conflict theo từng commit trong quá trình rebase.

\- Đưa lịch sử của nhánh `feature-api` lên trên đầu nhánh `main`.

\- Giữ lịch sử Git thẳng, không tạo merge commit phụ.



\---



\## 2. Khởi tạo repository



Khởi tạo Git:



```bash

git init

git branch -M main



Tạo file config.json ban đầu:

{

&#x20; "port": 8080,

&#x20; "debug": false

}



Commit:

git add config.json

git commit -m "up config"



3\. Tạo nhánh feature-api

Tạo nhánh:

git checkout -b feature-api



Commit thứ nhất trên feature-api

Sửa file config.json:

{

&#x20; "port": 9000,

&#x20; "debug": false

}



Commit:

git add config.json

git commit -m "update config"



Commit thứ hai trên feature-api

Tiếp tục sửa:

{

&#x20; "port": 9000,

&#x20; "debug": true

}



Commit:

git add config.json

git commit -m "feat: enable debug"



4\. Thay đổi trên nhánh main

Chuyển về main:

git checkout main



Commit thứ nhất trên main

Sửa port thành 8081:

{

&#x20; "port": 8081,

&#x20; "debug": false

}



Commit:

git add config.json

git commit -m "update port on main"



Commit thứ hai trên main

Thêm cấu hình môi trường:

{

&#x20; "port": 8081,

&#x20; "debug": false,

&#x20; "env": "production"

}



Commit:

git add config.json

git commit -m "add env config"



5\. Kiểm tra lịch sử trước khi Rebase

Chạy:

git log --graph --oneline --all



Kết quả trước khi rebase có dạng:

\* 01ca61d add env config

\* c103bf3 update port on main

| \* 7980620 feat: enable debug

| \* fe13f9a update config

|/

\* 60706bc up config



Có thể thấy lịch sử đang bị tách thành hai nhánh:

\- main

\- feature-api

6\. Thực hiện Rebase

Chuyển sang nhánh:

git checkout feature-api



Thực hiện:

git rebase main



Git phát hiện conflict tại file:

config.json



Thông báo:

CONFLICT (content): Merge conflict in config.json



7\. Conflict lần 1

Kiểm tra trạng thái:

git status



Kết quả:

interactive rebase in progress



Unmerged paths:



both modified: config.json



Nguyên nhân conflict:

\- main thay đổi port thành 8081.

\- feature-api thay đổi cùng dòng port thành 9000.

Mở config.json và xử lý thủ công.

Nội dung sau khi xử lý:

{

&#x20; "port": 9000,

&#x20; "debug": false,

&#x20; "env": "production"

}



Giải thích:

\- Giữ port = 9000 từ feature.

\- Giữ env = production từ main.

\- Không làm mất cấu hình mới trên main.

Sau đó:

git add config.json

git rebase --continue



Commit đầu tiên của feature được áp dụng thành công.

Ảnh minh chứng:

conflict-1.png



8\. Conflict lần 2

Git tiếp tục áp dụng commit:

feat: enable debug



Và tiếp tục phát sinh conflict tại:

config.json



Kiểm tra:

git status



Kết quả:

Unmerged paths:



both modified: config.json



Mở file và xử lý thủ công.

Nội dung cuối cùng:

{

&#x20; "port": 9000,

&#x20; "debug": true,

&#x20; "env": "production"

}



Giải thích:

\- port = 9000: giữ thay đổi của feature.

\- debug = true: giữ commit bật debug.

\- env = production: giữ cấu hình từ main.

Đánh dấu conflict đã được giải quyết:

git add config.json



Tiếp tục rebase:

git rebase --continue



Ảnh minh chứng:

conflict-2.png



9\. Kết quả sau khi Rebase

Sau khi xử lý hết conflict, Git hoàn tất quá trình rebase.

Kiểm tra:

git status



Kết quả mong đợi:

On branch feature-api



nothing to commit, working tree clean



10\. Kiểm tra lịch sử Git

Chạy:

git log --graph --oneline --all



Kết quả có dạng:

\* xxxx feat: enable debug

\* xxxx feat: change port

\* 01ca61d add env config

\* c103bf3 update port on main

\* 60706bc up config



Lịch sử Git đã trở thành một đường thẳng.

Các commit trên feature-api hiện nằm ngay sau các commit mới nhất của main.

Không có merge commit phụ.

Ảnh minh chứng:

after-rebase-graph.png



11\. So sánh Merge Conflict và Rebase Conflict

Merge

Khi dùng:

git merge



Git thường tạo ra một merge commit mới để kết hợp hai nhánh.

Lịch sử có thể xuất hiện nhiều nhánh giao nhau.

Ví dụ:

\*   Merge branch 'feature-api'

|\\

| \*

\* |

|/

\*



Rebase

Khi dùng:

git rebase main



Git sẽ lần lượt lấy từng commit của feature-api và áp dụng lại lên trên commit mới nhất của main.

Nếu một commit gây conflict, Git sẽ dừng lại tại chính commit đó.

Sau khi giải quyết:

git add config.json

git rebase --continue



Git mới chuyển sang commit tiếp theo.

Kết quả cuối cùng là lịch sử thẳng.

12\. Quy trình xử lý conflict trong Rebase

Quy trình thực hiện:

git rebase main

&#x20;       |

&#x20;       v

Conflict

&#x20;       |

&#x20;       v

Mở file và sửa thủ công

&#x20;       |

&#x20;       v

git add config.json

&#x20;       |

&#x20;       v

git rebase --continue

&#x20;       |

&#x20;       v

Conflict tiếp theo nếu có

&#x20;       |

&#x20;       v

Tiếp tục xử lý

&#x20;       |

&#x20;       v

Rebase hoàn tất



Nếu muốn hủy toàn bộ quá trình rebase:

git rebase --abort



Git sẽ quay lại trạng thái trước khi rebase.

13\. Các lệnh chính đã sử dụng

git init

git branch -M main



git add config.json

git commit -m "up config"



git checkout -b feature-api



git add config.json

git commit -m "update config"



git add config.json

git commit -m "feat: enable debug"



git checkout main



git add config.json

git commit -m "update port on main"



git add config.json

git commit -m "add env config"



git log --graph --oneline --all



git checkout feature-api



git rebase main



git status



git add config.json

git rebase --continue



git status



git add config.json

git rebase --continue



git log --graph --oneline --all

