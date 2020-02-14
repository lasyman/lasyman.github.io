---

title: Python之常用模块学习--click
date: 2020-02-13 12:29:17
categories: python模块
tags: python

---

# Python之常用模块学习--click

> Click是一个Python软件包，用于以可组合的方式创建漂亮的命令行界面，所需的代码更少。 这是“Command Line Interface Creation Kit”的缩写。 它是高度可配置的，但具有开箱即用的明智默认设置。
>
> 平时我们用Python写命令行工具，一般会用到argparse。那click就是argparse的升级版。那么我们看看click是怎么使用的。

<!--more-->

## 一、安装click

（注：click只支持Python3.4+和Python2.7）

```shell
pip install click
```

## 二、使用示例

### 1. 官方示例用法

```python
import click

@click.command()
@click.option("--count", default=1, help="Number of greetings.")
@click.option("--name", prompt="Your name",
              help="The person to greet.")
@click.option("--type", required=True, help="test type")
def hello(count, name):
    """Simple program that greets NAME for a total of COUNT times."""
    for _ in range(count):
        click.echo("Hello, %s!" % name)


if __name__ == '__main__':
    hello()

```

### 2. 多个参数

```python
import click

@click.command()
@click.option('--date', nargs=2, type=int)
def getdate(date):
    click.echo('%s年%s月' % date)


if __name__ == '__main__':
    getdate()

```



### 3. 限定参数范围

```python
import click

@click.command()
@click.option('-c', required=True, type=click.Choice(['start', 'stop']))
@click.option('-t', required=False, type=int)
def getcommand(c, t):
    """ simple example for click """
    click.echo('command is %s, timeout is %s' % (c, t))


if __name__ == '__main__':
    getcommand()

```

### 4. 分组参数

```python
import click
#命令行参数识别简写
CONTEXT_SETTINGS = dict(help_option_names=['-h', '--help'])

@click.group(context_settings=CONTEXT_SETTINGS)
def cli():
    """A simple command line tool."""


@cli.command('init', short_help='init the repo')
def init():
    """Initializes the repository."""


@cli.command('delete', short_help='delete the repo')
def delete():
    """Deletes the repository."""


if __name__ == '__main__':
    cli()

```

操作参数类型：

```
default： 设置命令行参数的默认值
help：参数说明
type：参数类型，可以是string、int、float等，也可以限定范围click.Choice['a','b']
prompt：当在命令行中没有输入相应的参数时，会更具prompt提示用户输入
nargs：指定命令行参数接受的值的个数
required：是否为必填参数
nargs: 参数个数
```



## 三、帮助文档

更多用法参考官方说明文档

https://click.palletsprojects.com/en/7.x/documentation/#documenting-arguments

