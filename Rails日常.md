## Rails日常

### 如何使用Rails+will_paginate+jQuery实现异步分页（加载更多）

**data属性的作用：存放数据，供js读取或暂时存放使用。**

HTML:

```html
<div id="users-list"></div>
<a href="javascript:;" data-next-page data-url="<%= users_path %>" data-container="#users-list" class="more-btn">更多</a>
```

JavaScript:

```javascript
$('.more-btn').click(function () {
  var $this = $(this),
      nextPage = $this.data('next-page') || 1;
  if (nextPage == -1)
    return;

  $.ajax({
    url: $this.data('url'),
    dataType: 'json',
    data: {
      page: nextPage,
    },
    beforeSend: function () {
      $this.data('ori-html', $this.html()).html('Loading...');
    },
    success: function (data) {
      $this.data('next-page', data.msg.next_page);
      if (data.msg.next_page == -1) {
        $this.addClass('nomore');
      }

      $($this.data('container')).append(data.msg.html);
    },
    complete: function () {
      $this.html($this.data('ori-html'));
    }
  })
})
.trigger('click');  // load first page when page loaded
```

Rails:

```ruby
def index
  # load data
  if request.xhr? #如果是异步请求
    @users = User.page(params[:page] || 1).per_page(params[:per_page] || 10)
      .order("id desc")

    render json: {
      status: :ok,
      msg: {
        html: render_to_string(partial: 'users', layout: false),
        next_page: (@users.blank? ? -1 : (@users.next_page || -1))
      }
    }
    return
  end
end
```

### Rails日志管理：logrotate的使用

显示日志：`ls -l cloud/current/log`

logrotate用来转储日志，linux发行版（云端服务器）都安装有该工具。

logrotate本身依赖于crond定时任务进程，在`/etc/cron.daily`下能看到logrotate配置文件。

`logrotate -f /etc/logrotate.conf` 会去logrotate.d文件夹下读取并运行配置文件

```
# /etc/logrotate.d/rails_log.conf
#
# rails_log.conf:
#
/var/www/app/current/log/*.log {
        daily
        size=200M
        rotate 10
        compress
        nodelaycompress
        missingok
        notifempty
        su deploy deploy # 压缩文件权限以及用户用户组
        create 664 deploy deploy # 创建新日志文件的权限以及用户用户组
        copytruncate # 避免服务器重启
}
```