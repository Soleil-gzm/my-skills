# Rails 技术写作补充

检测到 `Gemfile` 含 `rails` 时，在对应场景的通用规则之上叠加本文件。

## README 技术栈小节格式

```markdown
## Tech Stack

- **Framework**: Ruby on Rails 7.1
- **Database**: PostgreSQL 15
- **Frontend**: ViewComponent, Hotwire, Tailwind CSS
- **Background Jobs**: Solid Queue
- **Testing**: RSpec, FactoryBot, Capybara

## Prerequisites

- Ruby 3.3.0
- PostgreSQL 15+
- Node.js 20+

## Setup

​```bash
bundle install
bin/rails db:create db:migrate
bin/dev
​```

## Testing

​```bash
bundle exec rspec
​```
```

## API 文档：controller 行内注释

```ruby
# @api public
# @endpoint GET /api/v1/users
# @description 用户列表，带分页
#
# @param [Integer] page 页码（默认 1）
# @param [Integer] per_page 每页条数（默认 25）
#
# @response 200 [Array<User>] 用户列表
# @error 401 未认证
# @error 422 参数非法
def index
  @users = policy_scope(User).page(params[:page])
  render json: UsersSerializer.new(@users)
end
```

对外提供 API 的项目，可同时维护 OpenAPI：`swagger/v1/swagger.yaml`（openapi 3.0，paths + components/schemas）。

## YARD 行内文档

```ruby
# 创建用户账号
# @param [Hash] attributes 用户属性
# @option attributes [String] :email 邮箱
# @option attributes [String] :password 密码
# @return [User, nil] 创建的用户
# @raise [ValidationError] 校验失败时
def create_user(attributes)
  # ...
end
```

## ADR（架构决策记录）

放 `docs/adr/`，编号递增：

```markdown
# ADR 001: 用 ActiveInteraction 封装业务逻辑

## Status
Accepted

## Context
{需要解决的问题和约束}

## Decision
{决定}

## Consequences
### Positive / ### Negative

## Implementation
{gem、目录、命名约定}
```

## 注释原则

**写**：为什么这么做（而非做了什么）、复杂业务逻辑、bug workaround、性能优化点、安全考量、public API。
**不写**：显而易见的代码、重复信息、变更历史（那是 git 的事）、注释掉的死代码。

## Rails 项目变更时的检查单

- [ ] README（特性/Setup 变了）
- [ ] API docs（endpoint 变了）
- [ ] 行内注释（复杂逻辑变了）
- [ ] ADR（做了架构决策）
- [ ] CHANGELOG（任何面向用户的变更）
- [ ] Migration guide（破坏性变更）
