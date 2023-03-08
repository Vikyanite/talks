# Casbin使用教程

Casbin的强大之处以及我们为什么要学习Casbin都已经写在Casbin的[官网](https://casbin.org/zh/))了。

这篇文章主要从小白的角度去讲述，该如何在**Golang中使用Casbin实现RBAC**

本文目的主要是为了让自己明白Casbin的流程以及如何使用，作者本身也是小白，可能理解不到位还请见谅。

## Casbin的基本概念

Casbin的主要原理是一篇论文，如果想了解Casbin背后的底层原理可以读一下：[PML: An Interpreter-Based Access Control Policy Language for Web Services （PML：基于解释器的Web服务访问控制策略语言）](https://arxiv.org/abs/1903.09756)

这里简单讲一下Casbin中提到的基本概念，也就是**PERM (Policy, Effect, Request, Matcher)**模型。

### Request 请求

基本请求是一个元组对象，至少需要主题(访问实体 subject)、目标(访问资源 object) 和动作(访问方式 action)

例如，一个请求可能长这样： `r={sub,obj,act}`

<u>用人话来讲就是，一个请求起码要包含**发起请求的人，向什么请求，以及要干什么**</u>

### Policy 规则

定义访问规则的模式。 主要在策略规则文件中界定了字段的名称和顺序。

例如： `p={sub, obj, act}` 或 `p={sub, obj, act, eft}`

<u>用人话来说，就是你可以认为policy定义了一个func (sub, obj, act)</u>

### Matcher 匹配器

匹配请求和政策的规则。

例如： `m = r.sub == p.sub && r.act == p.act && r.obj == p.obj` 这个简单和常见的匹配规则意味着如果请求的参数(访问实体，访问资源和访问方式)匹配， 如果可以在策略中找到资源和方法，那么策略结果（`p.eft`）便会返回。 策略的结果将保存在 `p.eft` 中。

<u>用人话来说，就是用来制定匹配的规则，如`r.sub == p.sub && r.act == p.act && r.obj == p.obj`就意味着规则是r的三个东西都与p对上，又比如`r.sub == p.sub && r.act == p.act && r.obj == p.obj|| r.sub == "root"`就意味着要么都匹配上，要么是管理员。</u>

### Effect 效果

它可以被理解为一种模型，在这种模型中，对匹配结果再次作出逻辑组合判断。

例如： `e = some (where (p.eft == allow))`

这句话意味着，如果匹配的策略结果有一些是允许的，那么最终结果为真。

<u>用人话来说，如果只有上面三个东西的话我们只知道是不是匹配上了，以及匹配上了多少条，如果当你有多条policy被request匹配上了，有些是allow有些是deny，你要怎么知道该返回true还是false？这时候effect就起作用了。</u>

将他们全部组合在一起就成了Casbin的conf也就是**Model**

### Model Casbin模型

```
[request_definition]
r = sub, obj, act

[policy_definition]
p = sub, obj, act

[policy_effect]
e = some(where (p.eft == allow))

[matchers]
m = r.sub == p.sub && r.act == p.act && r.obj == p.obj
```

以上就是最基本的Casbin相关概念了

## RBAC与Casbin

RBAC就是带有角色关系的权限控制模型，也拥有几种变式，我这里就只需要用到最基本的RBAC就行，其他的不多作介绍。

在Casbin中实现RBAC，相较于一般的ACL（用户、资源、权限）的Model主要是多了`[role_definition]`这一部分，现在Model就变成了：

```ini
[request_definition]
r = sub, obj, act

[role_definition]
g = _, _

[policy_definition]
p = sub, obj, act

[policy_effect]
e = some(where (p.eft == allow))

[matchers]
m = r.sub == p.sub && r.act == p.act && r.obj == p.obj
```

官方对于这部分写的较为详细，直接贴过来了：

```ini
[role_definition]
g = _, _
g2 = _, _
```

上述角色定义表明, `g` 是一个 RBAC系统, `g2` 是另一个 RBAC 系统。 `_, _`表示角色继承关系的前项和后项，即前项继承后项角色的权限。 一般来讲，如果您需要进行角色和用户的绑定，直接使用`g` 即可。 当您需要表示角色（或者组）与用户和资源的绑定关系时，可以使用`g` 和 `g2` 这样的表现形式。

在Casbin里，我们以policy表示中实际的用户角色映射关系 (或是资源-角色映射关系)，例如:

```text
p, data2_admin, data2, read
g, alice, data2_admin
```

这意味着 `alice` 是角色 `data2_admin`的一个成员。 `alice` 在这里可以是用户、资源或角色。 Cabin 只是将其识别为一个字符串。

接下来在matcher中，应该像下面的例子一样检查角色信息：

```ini
[matchers]
m = g(r.sub, p.sub) && r.obj == p.obj && r.act == p.act
```

然后在匹配器中，我们不需要判断`r.sub`与`p.sub`完全相等，只需要使用`g(r.sub, p.sub)`来判断请求主体`r.sub`是否属于`p.sub`这个角色即可。这个`g(r.sub, p.sub)`的意思就是相当于`GetRole(r.sub) == p.sub`

