---
layout: post
title: Python 2.7 生成csv文件
tags: Python
---

{{ page.title }}
================

python中又csv库，直接import进来就可以，操作起来也比较方便：

<pre>
    <code>
        import os
        import csv

        def _build_csv():
            # csv title
            columns = ["name", "age"]

            # csv contents
            contents = [["Tom", 20],["Jack", 21],["Mary", 18]]

            # get current path
            root_path = os.getcwd()

            # the whole path of this csv file
            tmp_path = root_path + "/temp.csv"

            # open this file
            f = open(tmp_path, "wb+")

            # write bom to resolve unreadable chinese problem
            f.write("\xEF\xBB\xBF")
            writer = csv.writer(f)

            # write csv title
            writer.writerow(columns)

            # write csv content
            for line in contents:
                writer.writerow(line)

        if __name__="__main__":
            _build_csv()

    </code>
</pre>

这样我们就写了一个叫做temp.csv的文件，```f.write("\xEF\xBB\xBF")``` 是为了解决乱码问题。
就这样。

#####转载请注明出处[ivan's view](http://blog.ivan706.com/2014/07/02/angularjstemplatechangesymbol.html)，谢谢。
