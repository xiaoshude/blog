# 使用 prisma 操作数据库

## 背景

ORM：Object-relational mapping，是把对象和关系型数据库建立映射的过程。
实际应用开发中，基本都会引入 ORM 来辅助操作数据库，通常被提及的好处，例如：

- 可移植性，方便的切换数据库；
- 比直接写 SQL 效率高，方便多表联查；
- 可维护性高，和业务代码一致
...

和所有的技术选型一样，没有一种尺寸，适合所有的情况，用 ORM 也是如此。

下面看看操作数据库都有哪些途经，以及利弊。

## 操作数据库的途经

### High level: ORM

使用 ORM 的好处，前面已经提到。但实际应用开发中，不是所有的好处都成立，比如可移植性，目前还没有开发中途换数据库的情况，换数据库表明前期的方案设计有致命缺陷。

而且，如果是下面的原因，更应该慎重选择 ORM：

- 避免学习背后的 SQL

这无疑加大应用开发时的挫败感。每个报错，都会逼迫深入 SQL，甚至储存原理，而且你总会碰到报错。

此外，关于 ORM 的用法经验，很难跨语言迁移，甚至跨库迁移都不容易，想象下 sequlize 和 typeORM。

下次再想走捷径的时候，想象站在陡峭的高山下：
面前一个艰险的云梯，直上直下；一个盘山公路，平坦宽阔。选择走哪个？

**走最难的路 —— 直上直下的云梯，才是路径最短的路。**

- 表达力有限

不过这个可能很少遇到，尤其是 where 中存在子查询场景。

如果因为上面或者其他原因，需要剥开 ORM 的抽象，继续下探，Query builder 是一个不错的过渡。

## Middle level: Query builder

可能有激进派直接从 ORM 跳到了 raw sql，后果不过是积累了自己的 query builder。

对于通常的应用开发，几乎没有人，不会在写 raw sql 时，感到某种重复。可能以某些 utils 开始消除重复，慢慢 utils 汇聚成了 query builder。

如果到了这一层，还是使用比较成熟点的 query builder: Knex.js。

## Low level: Database driver

这一层，也称为 raw sql。
不管是因为 ORM 表现力不够，还是需要某种更底层的特性（connection pool?)，总之，要试 node-mysql2

https://github.com/sidorares/node-mysql2/tree/master/documentation

很多封装的便利都没了 缓存？防注入？执行效率？唯一索引？

经常 explain 下没有坏处。
