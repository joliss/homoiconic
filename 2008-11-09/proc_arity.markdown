Proc#arity in Ruby 1.8
---

[The docs for Proc#arity](http://ruby-doc.org/core/classes/Proc.html#M001577) state:

> `prc.arity → fixnum`: Returns the number of arguments that would not be ignored. If the block is declared to take no arguments, returns 0. If the block is known to take exactly n arguments, returns n. If the block has optional arguments, return -n-1, where n is the number of mandatory arguments. **A proc with no argument declarations is the same a block declaring || as its arguments.**

	Proc.new {}.arity          #=>  0
	Proc.new {||}.arity        #=>  0
	Proc.new {|a|}.arity       #=>  1
	Proc.new {|a,b|}.arity     #=>  2
	Proc.new {|a,b,c|}.arity   #=>  3
	Proc.new {|*a|}.arity      #=> -1
	Proc.new {|a,*b|}.arity    #=> -2
	
Let's try it:

	raganwald:2008-11-07 raganwald$ ruby -v
	ruby 1.8.6 (2008-03-03 patchlevel 114) [universal-darwin9.0]
	raganwald:2008-11-07 raganwald$ irb
	>> Proc.new {}.arity
	=> -1
	>> Proc.new {||}.arity 
	=> 0
	>> Proc.new {|*a|}.arity
	=> -1

I guess I need to find another way of detecting a proc with no argument declarations. At least until [known issue 574](http://redmine.ruby-lang.org/projects/ruby/issues?format=pdf "Ruby Issues [PDF]") is resolved.

**update**

I thought I'd have a look at method arity as well:

	class Foo
  
	  def no_args
	  end
  
	end

	Foo.module_eval { define_method :no_args_2 do; end }

	Foo.instance_method(:no_args).arity
	  => 0
	Foo.instance_method(:no_args_2).arity
	  => -1

It seems that if you explicitly define a method taking no parameters, you get the correct arity. However, if you use `define_method` and a block, you get the bug again.

---

Recent work:

* [Kestrels, Quirky Birds, and Hopeless Egocentricity](http://leanpub.com/combinators) and my [other books](http://leanpub.com/u/raganwald).
* [Cafe au Life](http://recursiveuniver.se), a CoffeeScript implementation of Bill Gosper's HashLife written in the [Williams Style](https://github.com/raganwald/homoiconic/blob/master/2011/11/COMEFROM.md).
* [Katy](http://github.com/raganwald/Katy), a library for writing fluent CoffeeScript and JavaScript using combinators.
* [Method Combinators](https://github.com/raganwald/method-combinators), a CS/JS library for writing method decorators, simply and easily. 

---

[Reg Braithwaite](http://braythwayt.com) | [@raganwald](http://twitter.com/raganwald)