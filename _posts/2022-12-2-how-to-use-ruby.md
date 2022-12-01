---
layout: post
title: ruby入门
tags: 后端
categories: 后端
---

第一个程序、定义类、打印方法、判断是否有方法属性、放开属性、数组、注释、字符串操作、字典、类型转换、静态方法、继承、模块、流程控制、循环、异常处理



# 第一个程序

```ruby
def sayHello
    puts 'hello word'
end

sayHello
```

# 定义类

```ruby
# 类名第一个字母必须大写
class Player
    def initialize(name = "zhanghuan")
        @name = name
    end
    def show()
        puts "hello,#{@name}"
    end
end

p = Player.new()
p.show()
```



# 打印方法

```ruby
puts Player.instance_methods(false)
```



# 判断是否有方法、属性

```ruby
if p.respond_to?("show")
    p.send("show")
end
```



# 放开属性

```ruby
class Game
    attr_accessor :price,:title #开放属性，外部可以访问
    def initialize(title = "game1", price = 200)
        @title = title
        @price = price
    end
    def show()
        puts "标题：#{@title}"
        puts "价格：#{@price}"
    end
end

g = Game.new()

puts g.title #可访问

g.title = "game2"

puts g.title #可修改
```



# 数组

```ruby
games = ["绝地求生","怪物猎人","埃尔登法环"]

# 输出
puts games

# 遍历
games.each do |game|
    puts "我爱 #{game}"
end

games.each_with_index do |game,i|
    puts "我爱 #{i} #{game}"
end

# 连接
puts games.join(",")

# 判断是否是数组
puts games.respond_to?("each")
```



# 注释

```ruby
# 行注释

=begin
多行注释
=end

__END__
结束语
```



# 字符串操作

```ruby
a = "hello"
b = "word"

# 字符串带入，相当于a = a + b
a << b
puts a

# 字符串重复
puts a * 4 

# 单引号和双引号的区别和shell脚本类似
```



# 字典

```ruby
dic1={
    "a":1,
    "b":2
}

dic2={
    name:"zhanghuan",
    age:"20"
}
puts dic1[:a]
puts dic2[:name]
```



# 类型转换

```ruby
a = "1"
b = "2"
c = "3"

puts a + b + c
# 字符串转数字
puts a.to_i + b.to_i + c.to_i
# 数字转字符串
puts (a.to_i + b.to_i + c.to_i).to_s + "AAA"
```



# 静态方法

```ruby
class Game
    def initialize(name = "zhanghuan")
        @name = name
    end
    def show()
        puts @name.to_s
    end
    # 定义静态方法
    def self.toStr()
        puts "aaaaaa"
    end
end

game = Game.new()
game.show()
# game.toStr() 不可以这样写，因为实例hi无法调用静态方法的

Game.toStr()
Game::toStr()
```



# 继承

```ruby
class SteamGame < Game
    def info
        puts "G胖"
    end
end

game = SteamGame.new()
game.info()
```



# 模块

```ruby

module BaseFunc
    Version = "1.0"
    def v
        return Version
    end
    def add(a,b)
        return a + b
    end
    def self.showVersion
        return Version 
    end
    # 定义静态方法
    module_function :v
end

puts BaseFunc.v
puts BaseFunc.showVersion
puts BaseFunc::Version
# BaseFunc.add(10,20) 不可用


class BaseClass include BaseFunc
end

# 类包含模块后，模块中的方法不可直接用，要实例化后才可用
#puts BasClass.v 不可用
#puts BasClass::showVersion 不可用
puts BaseClass::Version

myCls = BaseClass.new
puts myCls.add(10,20)
# puts myCls.v 不可用
```



# 流程控制

```ruby
point_per_game = 30

if point_per_game >= 30
    puts "3500万美元年薪"
elsif point_per_game >= 20
    puts "2500万美元年薪"
else
    puts "1500万美元年薪"
end

case point_per_game
    when 30
        puts "正好30"
    when 20
        puts "正好20"
    else
        puts "??"
    end
```



# 循环

```ruby
languages = ["java","python","ruby"]

for item in languages do
    puts item
end

languages.each{
    |item|
    puts item
}

languages.each_with_index do |item,i|
    puts i.to_s + "." + item
end

# times循环
5.times do |i|
    puts i
end

# step循环
1.step(10,2) do |i|
    puts i
end

# upto
2.upto(5) do |i|
    puts i
end

# downto
5.downto(2) do |i|
    puts i
end

# 包含5
for i in 1..5 do
    puts i
end

# 不包含5
for i in 1...5 do
    puts i
end

index = 0
while index < 5 do
    puts index
    index += 1
end

index = 0
until index == 5 do
    puts index
    index += 1
end
```



# 异常处理

```ruby
begin #try
    a = 1/0
rescue => e #catch
    puts e
else # 鸡肋
    puts "正常处理"
ensure # finally
    puts "扫尾"
end
```

