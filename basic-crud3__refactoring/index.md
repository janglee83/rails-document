# Basic CRUD Part 3: Refactoring

Tại phần này mình sẽ chỉ cho các bạn một số kỹ thuật refactoring, làm cho code của các bạn clean hơn!!

---
- [Basic CRUD Part 3: Refactoring](#basic-crud-part-3-refactoring)
  - [Partial](#partial)
  - [S.O.L.I.D](#solid)
---

## Partial

1. Create Partial Collections
     - Như bạn biết đến Partial, khi bạn mở rộng hệ thống, bạn cần hiển thị comment collection, sẽ có thể bạn phải tái sử dụng code hiển thị 1 comment, thì bạn cần tạo một component comment để hiển thị, sau đó bạn muốn hiển thị bao nhiêu lần cũng được:
     - Tạo file `views/comments/_comment.html.erb` với nội dung dưới đây:

      ```erb
        <p>
          <strong>Commenter:</strong>
          <%= comment.commenter %>
        </p>

        <p>
          <strong>Comment:</strong>
          <%= comment.body %>
        </p>
      ```

     - Bây giờ bạn muốn hiển thị comment tại đâu cũng được, chỉ cần render ra partial trên bằng cú pháp `<%= render @article.comments %>`, bạn đọc muốn hiểu vì sao có thể viết như vậy bạn đọc [tại đây](https://api.rubyonrails.org/classes/ActionView/PartialRenderer.html)

2. Rendering a Partial Form
   - Tương tự như ý trên, bạn có thể gộp form trong `articles/show.html.erb` thành partial:
   - Tạo một file `views/comments/_form.html.erb` với nội dung như sau:

      ```erb
        <%= form_with model: [ @article, @article.comments.build ] do |form| %>
          <p>
            <%= form.label :commenter %><br>
            <%= form.text_field :commenter %>
          </p>
          <p>
            <%= form.label :body %><br>
            <%= form.text_area :body %>
          </p>
          <p>
            <%= form.submit %>
          </p>
      <% end %>
      ```
   - Tại `views/articles/show.html.erb` bạn có thể goi cái form trên bằng cú pháp `<%= render 'comments/form' %>`

      *CHÚ Ý: Trong Partial, bạn không nhất thiết phải truyền tham số cho partial vì bạn hoàn toàn có thể sử dụng chung trong view vì nó cùng render ra, tuy nhiên mình không recomend cách làm này vì có thể cái partial đó được sử dụng chỗ khác với các biến khác nhau*

## S.O.L.I.D

Khi hệ thống của bạn mở rộng đến một mức nào đó, thì việc bạn cần thiết kế hệ thống của bạn theo các chuẩn SOLID là điều cần thiết. Bài giảng về solid bạn có thể dùng của Prof.Nguyen Thi Thu Trang [tại đây](https://www.dropbox.com/sh/goc6uhiw0t0ppp6/AACNmkdJdLHE9bUY8p3xSwzoa/Lecture?dl=0&preview=11-Design+Principles.pdf&subfolder_nav_tracking=1). Sau đây mình sẽ mở rộng case study để mọi người có thể hiểu về SOLID.

1. New statements

      **Bạn được yêu cầu tạo một feature cho dự án của mình, có tên gọi là statuses. Một article hoặc comment có thể có rất nhiều statuses, và status đó có thể public tới mọi người, tuy nhiên nó có thể private, tức chỉ người viết article đó nhìn thấy, hoặc nó có thể bị hiden tới cả 2 role, tuy nhiên vẫn được retrievable**

2. Phân tích yêu cầu
   - Vì mỗi comment hoặc article đều có status, bạn phải tạo cột status tới database.
   - Bạn cần định nghĩa các status: `['public', 'private', 'archived']`
   - Với từng trạng thái thì các article và comment sẽ như nào?

3. Concern
   - Trong rails, giới thiệu một khái niệm gọi là `Concern`, `Concern` có tác dụng mạnh nhất khi controller và model quá lớn, cung cấp một cách viết code dễ hiểu hơn nhiều.
   - Trong concern, file được tổ chức theo dạng module hoặc class tuỳ chủ đích của bạn. Trong phần này mình sẽ viết bằng 2 cách.

4. Thực hành:

    4.1. Tạo cột status:
     -  Bạn chạy các lệnh dưới đây:

        ```bash
          rails generate migration AddStatusToArticles status:string
          rails generate migration AddStatusToComments status:string
        ```
     - Sau đó bạn chạy:

        ```bash
          rails db:migarte
        ```
     - Tại cả hai `comments_controller.rb` và `articles_controller.rb`, bạn thay đổi strong params, thêm trường `:status` như dưới đây:

        ```ruby
          private
          def article_params
            params.require(:article).permit(:title, :body, :status)
          end
        ```
       - Đoạn code này mình đã giải thích khá nhiều rồi nên bạn đọc lại các topic trước để hiểu hơn nhé.
     - Trong cả 2 model `Comment.rb` và `Article.rb`, bạn thêm các dòng code này sau:

        ```ruby
          VALID_STATUSES = ['public', 'private', 'archived']

          validates :status, inclusion: { in: VALID_STATUSES }

          def archived?
            status == 'archived'
          end
        ```
        - `VALID_STATUES` là biến kiểu constant, chỉ đơn giản là khai báo thôi
        - Interface [`validates`](https://api.rubyonrails.org/classes/ActiveModel/Validations/ClassMethods.html#method-i-validates) đã được rails giải thích với ví dụ dễ hỉu rồi nè, không hiểu đọc tại đây nhé.
        - `archived?` chỉ đơn giản là khai báo function với giá trị trả về là **Boolean**. *Chú ý: các hàm trả về giá trị Boolean nên đặt tên với dấu ? ở đằng sau*
    - Tại `app/views/articles/index.html.erb`, bạn update code như dưới đây

        ```erb
          <h1>Articles</h1>
          <ul>
            <% @articles.each do |article| %>
              <% unless article.archived? %>
                <li>
                  <%= link_to article.title, article %>
                </li>
              <% end %>
            <% end %>
          </ul>

          <%= link_to "New Article", new_article_path %>
        ```
        - Vì bạn đã update code như trên rồi, bạn có thể kiểm tra nếu `status = 'archived'` thì ẩn `archived` đi, còn không thì hiển thị
    - Tại `app/views/comments/_comment.html.erb`, bạn update code như dưới đây:

        ```erb
          <% unless comment.archived? %>
            <p>
              <strong>Commenter:</strong>
              <%= comment.commenter %>
            </p>

            <p>
              <strong>Comment:</strong>
              <%= comment.body %>
            </p>
          <% end %>
        ```
         - Tương tự như trên, chỉ đơn giản là check điều kiện

    4.2. Updated code:
      - Khi bạn viết đoạn code trên, bạn sẽ thấy các model bị lặp lại đoạn code khá dài, đúng chứ, thế nên sự tồn tại của `concern` sẽ giải quyết điều này. Nếu bạn đơn giản có đoạn code bị lặp lại thì bạn có thể ném vào `concern` để tái sử dụng chúng.

      - Các bạn tạo file `app/models/concerns/visible.rb` với nội dung như sau:

          ```ruby
          module Visible
            extend ActiveSupport::Concern

            VALID_STATUSES = ['public', 'private', 'archived']

            included do
              validates :status, inclusion: { in: VALID_STATUSES }
            end

            def archived?
              status == 'archived'
            end
          end
          ```

        - Giải thích: Các bạn tạo một module chứa các đoạn code giống nhau
        - Tuy nhiên, các bạn khi muốn sử dụng các interface mà ở cấp class thì các bạn nên extend `ActiveSupport::Concern`, lý do thì bạn cần phải hiểu cách hoạt động của rails bằng việc đọc [Ruby singleton class](https://viblo.asia/p/ruby-singleton-class-ByEZkGMAZQ0) và [Ruby module: Include, Prepend và Extend](https://viblo.asia/p/ruby-module-include-prepend-va-extend-GrLZDkdgKk0) và [ActiveSupport::Concern API](https://api.rubyonrails.org/v7.0.4.2/classes/ActiveSupport/Concern.html) để hiểu rõ vì sao lại làm như vậy.
      - Tại các model khác, bạn đơn giản chỉ cần viết `import Visible` ở trong block class để import các operation và variable tương ứng.
      - Ngoài ra, nếu bạn muốn thêm các function cấp class (có thể truy cập bởi các object) thì bạn có thể thêm đoạn code này vào module `Visible` như sau:

        ```ruby
          class_methods do
            def public_count
              where(status: 'public').count
            end
          end
        ```
      - Lý do vì sao có thể viết thế này mình sẽ để giải thích topic tiếp theo.
