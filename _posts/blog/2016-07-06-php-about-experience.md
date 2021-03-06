---
layout:   post
title:    关于PHP一些知识点整理
description: 关于PHP一些知识点整理，仅代表个人观点，如有错误，底部留言
category: blog
---

## Nginx 和 Apache 一些观点

* Nginx轻量级，跑同样的PHP应用占用的内存和资源比Apache少
* Nginx抗并发，Nginx的请求处理是异步非阻塞，而Apache是阻塞型，所以Nginx在高并发下能保持低资源、低消耗、高性能
* Nginx是异步，多个连接对应一个进程；Apache是同步多进程，一个连接对应一个进程
* Nginx高度模块化设计，社区活跃，各种高性能模块出产的速度比较快
* Nginx本身就是反向代理服务器，支持7层负载均衡
* Apache的rewrite比Nginx强大
* Apache模块多，bug少，超级稳定

*各有个的好处自己判断吧*

## 关于Redis和Memcached一些观点

Memcached的内部内存管理机制虽然不像Redis的那样复杂，但却更具实际效率——这是因为Memcached在处理元数据时所消耗的内存资源相对更少。作为Memcached所支持的惟一一种数据类型，字符串非常适合用于保存那些只需要进行读取操作的数据，因为字符串本身无需进行进一步处理。除此之外，Memcached在横向扩展方面也比Redis更具优势。由于其在设计上的思路倾向以及相对更为简单的功能设置，Memcached在实现扩展时的难度比Redis低得多。

Redis则在缓存管理工作中表现是非卓越，这套缓存方案采用所谓数据回收机制，能够将陈旧数据从内存中删除以提供新数据所必需的缓存空间。Memcached的数据回收机制使用的是LRU(即最低近期使用量)算法，而且往往会比较武断地直接删除掉与新数据体系相近的原有内容。相比之下，Redis允许用户更为精准地进行细化控制，利用六种不同回收策略确切提高缓存资源的实际利用率。Redis还采用更为复杂的内存管理与回收对象备选方案。

此外，Memcached将键名限制在250字节，值也被限制在不超过1MB，且只适用于普通字符串。Redis则将键名与值的最大上限各自设定为512MB，且支持二进制格式。Redis支持六种数据类型，因此能够更加智能地对数据进行缓存处理及操作，这相当于为应用程序开发人员敞开了一道通往无尽可能性的大门。Redis散列机制的存在保证开发人员无需经历获取完整字符串、反序列化、更新值、对象重新序列化并在每次值更新后利用其替代缓存内完整字符串这一系列复杂的流程——这也意味着资源消耗量得以降低、性能表现迎来显著提升。Redis所支持的其它数据类型，例如Lists以及Sets——也可被用于实现更加复杂的缓存管理模式。

Redis所保存的数据透明化，服务器能够直接对数据进行操作，支持的命令较多。还提供了数据持久化的方案。

## 关于MySQL存储引擎一些观点

* MySQL默认的存储引擎是MyiSam，其不支持外键，不支持事务，但访问速度快
* InnoDB具有提交、回滚等事务，但写入效率低，并且占用更多的存储空间用来保留数据和索引
* Memory将所有数据保存在RAM中，需要查找、引用类似数据环境下，可提供极快的访问
* Merge存储引擎是一组MyISAM表的组合，这些MyISAM表必须结构完全相同。MERGE表本身没有数据，对MERGE类型的表进行查询、更新、删除的操作，就是对内部的MyISAM表进行的。

***

MyISAM支持静态表、动态表、压缩表三种存储格式，默认是静态表。静态表中的字段都是非变长的字段，存储速度快、出现故障容易恢复，但占用空间比动态表通常要多；动态表字段是变长的，占用空间相对较少，但频繁操作表会产生碎片，出现故障时恢复相对困难；压缩表占用磁盘空间小，每条记录都是单独压缩，所以访问开支较小。

InnoDB存储方式使用共享表空间存储和使用多表空间两种。

## 关于创建索引的一些观点

* 虽然索引大大提高了查询速度，同时却会降低更新表的速度，如对表进行INSERT、UPDATE和DELETE。因为更新表时，MySQL不仅要保存数据，还要保存一下索引文件。
* 建立索引会占用磁盘空间的索引文件。一般情况这个问题不太严重，但如果你在一个大表上创建了多种组合索引，索引文件的会膨胀很快。
* 一般情况下不鼓励使用like操作，如果非使用不可，如何使用也是一个问题。like “%aaa%” 不会使用索引而like “aaa%”可以使用索引。

##isset和array_key_exists区别

* array_key_exists

原型：bool array_key_exists ( mixed key, array search )

描述：给定的key存在于数组中时返回TRUE，即使key对应的键值为NULL也返回true。array_key_exists() 也可用于对象

* empty

原型：bool empty ( mixed var )

描述：如果 var 是非空或非零的值，则 empty() 返回 FALSE。换句话说，""、0、"0"、NULL、FALSE、array()、var $var; 以及没有任何属性的对象都将被认为是空的，如果 var 为空，则返回 TRUE。

注意：

empty是语句而不是函数；

empty只能用于变量，诸如empty(addslashes($var))的用法是错误的，因为addslashes($var)返回常量。

* isset

原型：bool isset ( mixed var [, mixed var [, ...]] )

描述：如果var存在则返回TRUE，否则返回FALSE；若使用 isset()测试一个被设置成 NULL 的变量，将返回 FALSE。

注意：

isset是语句而不是函数；

isset只能用于变量，若想检测常量是否设置可以使用defined()函数。

使用unset实际上就是将var置为NULL。

> 性能比较：

结论：isset ~ empty > array_key_exists
原因：isset和empty是语句，而array_key_exists是函数，后者比前者多了函数调用，因此性能上要稍差。而isset和empty的范围是不一样的，主要区别在于值为NULL的情况，需要特别注意。

## 远程获取图片大小

```
public function test()
    {
        ob_start();
        $ch = curl_init("https://dn-phphub.qbox.me/uploads/banners/0pyH7UgXhF7PTBkLZRak.png?imageView2/1/w/424/h/212");
        curl_setopt($ch, CURLOPT_HEADER, 1);
        curl_setopt($ch, CURLOPT_NOBODY, 1);
        $okay = curl_exec($ch);
        curl_close($ch);
        $head = ob_get_contents();
        ob_end_clean();
        $regex = '/Content-Length:\s([0-9].+?)\s/';
        $count = preg_match($regex, $head, $matches);
        echo isset($matches[1]) ? $matches[1] : 'unknown';
    }
```

## 不用内置函数取代var_dump()

```
function dump($var, $label = '', $return = false)
{
    if(ini_get('html_errors')){
        $content = "<pre>\n";
        if($label != ''){
            $content .= "<strong>{$label}</strong>\n";
        }
        $content .= htmlspecialchars(print_r($var, true));
        $content .= "\n</pre>\n";
    }else{
        $content .= $label . ":\n" . print_r($var,true);
    }
    if($return) return $content;
    echo $content;
    return null;
}
```

