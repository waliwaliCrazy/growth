<h1> Redis-string操作 </h1>

---

- [Redis 五大核心数据类型](#redis-五大核心数据类型)
- [string 字符串命令](#string-字符串命令)
	- [设置值](#设置值)
	- [修改值](#修改值)
	- [set 设置指定的key的值](#set-设置指定的key的值)
	- [setnx 命令在指定的 key 不存在时，为 key 设置指定的值](#setnx-命令在指定的-key-不存在时为-key-设置指定的值)
	- [get 获取 key 的值](#get-获取-key-的值)
	- [getset 命令用于设置指定 key 的值，并返回 key 旧的值](#getset-命令用于设置指定-key-的值并返回-key-旧的值)
	- [setex 命令为指定的 key 设置值及其过期时间。如果 key 已经存在， SETEX 命令将会替换旧的值。](#setex-命令为指定的-key-设置值及其过期时间如果-key-已经存在-setex-命令将会替换旧的值)
	- [setex 命令以毫秒为单位设置 key 的生存时间。](#setex-命令以毫秒为单位设置-key-的生存时间)
	- [Mget 命令返回所有(一个或多个)给定 key 的值。 如果给定的 key 里面，有某个 key 不存在，那么这个 key 返回特殊值 nil 。](#mget-命令返回所有一个或多个给定-key-的值-如果给定的-key-里面有某个-key-不存在那么这个-key-返回特殊值-nil-)
	- [Mset 命令用于同时设置一个或多个 key-value 对。](#mset-命令用于同时设置一个或多个-key-value-对)
	- [Getrange 命令用于获取存储在指定 key 中字符串的子字符串。字符串的截取范围由 start 和 end 两个偏移量决定(包括 start 和 end 在内)。](#getrange-命令用于获取存储在指定-key-中字符串的子字符串字符串的截取范围由-start-和-end-两个偏移量决定包括-start-和-end-在内)
	- [Getbit 命令用于对 key 所储存的字符串值，获取指定偏移量上的位(bit)。](#getbit-命令用于对-key-所储存的字符串值获取指定偏移量上的位bit)
	- [Setbit 命令用于对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。](#setbit-命令用于对-key-所储存的字符串值设置或清除指定偏移量上的位bit)
	- [Setrange 命令用指定的字符串覆盖给定 key 所储存的字符串值，覆盖的位置从偏移量 offset 开始。](#setrange-命令用指定的字符串覆盖给定-key-所储存的字符串值覆盖的位置从偏移量-offset-开始)
	- [Strlen 命令用于获取指定 key 所储存的字符串值的长度。当 key 储存的不是字符串值时，返回一个错误。](#strlen-命令用于获取指定-key-所储存的字符串值的长度当-key-储存的不是字符串值时返回一个错误)
	- [Msetnx 命令用于所有给定 key 都不存在时，同时设置一个或多个 key-value 对。](#msetnx-命令用于所有给定-key-都不存在时同时设置一个或多个-key-value-对)
	- [incr 命令将 key 中储存的数字值增一。](#incr-命令将-key-中储存的数字值增一)
	- [incrby 命令将 key 中储存的数字加上指定的增量值。](#incrby-命令将-key-中储存的数字加上指定的增量值)
	- [incrbyfloat 命令为 key 中所储存的值加上指定的浮点数增量值。](#incrbyfloat-命令为-key-中所储存的值加上指定的浮点数增量值)
	- [decr 命令将 key 中储存的数字值减一。](#decr-命令将-key-中储存的数字值减一)
	- [decrby 命令将 key 所储存的值减去指定的减量值。](#decrby-命令将-key-所储存的值减去指定的减量值)
	- [Append 命令用于为指定的 key 追加值。](#append-命令用于为指定的-key-追加值)

---

# Redis 五大核心数据类型

- 字符串（strings）
- 散列（hashes）
- 列表（lists）
- 集合（sets）
- 有序集合（sorted sets）

string 是 Redis 最基本的类型

Redis 的 string 可以包含任何数据:图片(二进制数据),序列化的对象,单个 value 值最大的上限是1GB

	set key value
		* 保存嘛,值类型是string
	mset key1 value1 key2 value2 ..keyn valuen 
		* 一次设置N个
	mset key1 key2...keyn
		* 一次获取多个
	incr key
		* 对key的值做++操作后,返回新值
		* 如果key不是数字的话,会报错
		* 可以操作新key,或者旧key.如果是新key,那很显然值就是1了
	decr key
		* 对key的值做--操作后,返回新值
		* 同上
	incrby key Integer
		* 对指定key,添加指定的值
		* 同上
	decrby key Integer
		* 对指定key减去指定值
		* 同上
	append key value
		* 对指定key追加指定的value
	substr key start end
		* 返回截取后的字符串
		* 从第几个字符开始,到第几个字符结束,包含开始位置,也包含结束位置

# string 字符串命令

## 设置值

## 修改值



## set 设置指定的key的值
    
命令: set keyname value

## setnx 命令在指定的 key 不存在时，为 key 设置指定的值

setnx = set if not exists

命令: setnx keyname value

## get 获取 key 的值

命令: get keyname

## getset 命令用于设置指定 key 的值，并返回 key 旧的值

命令: getset keyname value

例如原 key1 = 'abc'

执行 `getset key1 123`, 返回 abc, 并将 key1 的值改为 123

## setex 命令为指定的 key 设置值及其过期时间。如果 key 已经存在， SETEX 命令将会替换旧的值。

## setex 命令以毫秒为单位设置 key 的生存时间。


## Mget 命令返回所有(一个或多个)给定 key 的值。 如果给定的 key 里面，有某个 key 不存在，那么这个 key 返回特殊值 nil 。

## Mset 命令用于同时设置一个或多个 key-value 对。


## Getrange 命令用于获取存储在指定 key 中字符串的子字符串。字符串的截取范围由 start 和 end 两个偏移量决定(包括 start 和 end 在内)。


## Getbit 命令用于对 key 所储存的字符串值，获取指定偏移量上的位(bit)。



## Setbit 命令用于对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。



## Setrange 命令用指定的字符串覆盖给定 key 所储存的字符串值，覆盖的位置从偏移量 offset 开始。

## Strlen 命令用于获取指定 key 所储存的字符串值的长度。当 key 储存的不是字符串值时，返回一个错误。


## Msetnx 命令用于所有给定 key 都不存在时，同时设置一个或多个 key-value 对。


## incr 命令将 key 中储存的数字值增一。

## incrby 命令将 key 中储存的数字加上指定的增量值。

## incrbyfloat 命令为 key 中所储存的值加上指定的浮点数增量值。

## decr 命令将 key 中储存的数字值减一。

## decrby 命令将 key 所储存的值减去指定的减量值。

## Append 命令用于为指定的 key 追加值。
