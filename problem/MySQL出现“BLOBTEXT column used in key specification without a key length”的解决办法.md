# MySQL出现“BLOB/TEXT column used in key specification without a key length”的解决办法

## 问题排查

在我用GORM保存对象的的时候，报了“BLOB/TEXT column used in key specification without a key length”的错误。经过网上查资料发现最主要原因是，我**把longtext字段设置为了unique**就导致了这次出错。

## 网上资料

> Mysql数据库对于BLOB/TEXT这样类型的数据结构**只能索引前N个字符**。所以这样的数据类型**不能作为主键**，**也不能是UNIQUE的**。所以要换成VARCHAR，但是VARCHAR类型的大小也不能大于255，当VARCHAR类型的字段大小如果大于255的时候也会转换成小的TEXT来处理。所以也同样有问题。
>
> 此外，因为MySQL只能将BLOB/TEXT类型字段设置索引为BLOB/TEXT数据的钱N个字符，因此错误常常发生在字段被定义为TEXT/BLOB类型或者和TEXT/BLOB同质的数据类型，如TINYTEXT,MEDIUMTEXT,LONGTEXT ,TINYBLOB,MEDIUMBLOB 和LONGBLOB，并且当前操作是将这个字段设置成主键或者是索引的操作。在未指定TEXT/BLOB‘键长’的情况下，字段是变动的并且是动态的大小所以MySQL不能够保证字段的唯一性。因此当使用TEXT/BLOB类型字段做为索引时，N的值必须提供出来才可以让MySQL决定键长，但是MySQL不支持在TEXT/BLOB限制，TEXT(88)是不行的。
>
> 当你试图将数据表中的一个非TEXT或者非BLOB类型如VARCHAR或ENUM的字段转换成TEXT/BLOB，同时这个字段已经被定义了unique限制或者是索引，这个错误也会弹出，并且更改数据表的命令会执行失败

## 解决办法

也就是longtext取消unique就好了