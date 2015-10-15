---
layout: post
title: Handling errors in elixir
description: Why the Internet everywhere is not the same as the Internet anywhere
date: 2015-10-15 14:56:05
tags: Ruby Design
author: Peter Saxton
---

### Being Contrary

This post is to extend a throw away comment I made on [twitter]() about returning tagged tuples as the result of any expression in elixir.

**Tempted so say all elixir funcs should return result tuple. `{:ok, 4} = 2 + 2`**

Obviously several people disagreed and a tweet was never going to be enough to explain my reasoning.

### Some background

- *Tuple:* A data structure consisting of multiple parts.
- *Tagged Tuple:* A tuple where the first part is an atom that acts as a label describing the remaining contents in the tuple.

Elixir has inherited the tagged tuple error handling convention from erlang.
Any function that could fail to compute will return a tuple tagged with the `:ok` atom and a value if all went well.
If the function could not return a value then it returns a tuple tagged `:error` and the reason.
This is then used in code by pattern matching on the tag and calling the behaviour you want

{% highlight elixir %}
case MyModule.flaky_method do
  {:ok, value} -> IO.puts "All good value was: #{value}."
  {:error, reason} -> IO.puts "Uh oh! Failed due to #{reason}."
end
{% endhighlight %}

Being a convention it can be ignored.

- Some functions simply return `nil` or `:error` instead of a tuple and a reason.
- Some functions can't fail so just return a value, as in my original tweet adding 2 numbers always works and so `2 + 2` will only return `4`.
- Some functions borrow from ruby conventions and have methods that can throw errors sometimes ending in an exclamtion mark.

### A problem to solve

So before presenting you with a solution it's good practise to have a problem.
For example I have a JSON file of information about people and I want to get out the information relating to me.
So the steps are read the file, parse the json and finally look for the bit about me.
We know we want to handle this as a list of instructions and probably as a pipeline.
Each of the steps might fail and we need to handle that.
The code with the error handling could end up looking something like this.

{% highlight elixir %}
result = case File.read("path/to/file.yaml") do
  {:ok, contents} ->
    case Poison.decode(contents) do
      {:ok, json} ->
        case Dict.fetch(json, "Peter") do
          {:ok, data} -> {:ok, data}
          :error -> {:error, :key_not_found}
          # Notice the Dict API doesn't follow convention and needs patching here.
        end
      {:error, reason} -> {:error, reason}
    end
  {:error, reason} -> {:error, reason}
end
{% endhighlight %}

Well thats ugly can we do this better?
Thinking a bit harder we can see a common pattern.
If a result is successful we want to take the value of the result and use it in the next step.
But if the result was an error then we want to stop processing and return.

### The Result module
The `Result` module handles passing values from `:ok` tagged tuples to functions and not calling functions with an `:error` tagged tuple.
The method of interest is bind (It is called bind to make further reading easier).

- If the result given is a success call the next function with the value.
- If the result is a failure don't invoke the function.

{% highlight elixir %}
defmodule Result do
  def bind({:ok, value}, func) when is_function(func, 1), do: func.(value)
  def bind(failure = {:error, _}, _func), do: failure
end
{% endhighlight %}

With the bind method we can then return to a linear set of steps.

{% highlight elixir %}
import Result

{:ok, "path/to/file.yaml"}
|> bind(&File.read/1)
|> bind(&Poison.decode/1)
|> bind(fn
  (%{"Peter": data}) -> {:ok, data}
  (_) -> {:error, :key_not_found}
  # This noise here is only necessary as the Dict returns :error without a reason
end)
{% endhighlight %}
