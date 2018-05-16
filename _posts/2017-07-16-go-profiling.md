---
layout: post
title: "Golang Profiling"
date: 2017-07-16
categories: tech
---

I am going to talk about golang profiling in this article.

All you need to do to profile your code performance is to import “net/http/pprof” package and add the line http.ListenAndServe(“:8081”, nil) if your application is based on http.

Here’s the test code which is an implementation of Finding the Max Subarry algorithm by using divide-and-conquer paradigm:

![divcon-debug0](https://raywangblog.files.wordpress.com/2017/07/divcon-debug0.png?w=1100)

![divcon-1](https://raywangblog.files.wordpress.com/2017/07/divcon-1.png?w=1100)

![divcon-2](https://raywangblog.files.wordpress.com/2017/07/divcon-2.png?w=1100)

First things first, the go tool pprof command will be used to profile.
```
$> go tool pprof  http://localhost:8081/debug/pprof/profile
```

![go-tool-pprof-httpaddr](https://raywangblog.files.wordpress.com/2017/07/go-tool-pprof-httpaddr.png?w=1100)

Here, we can view the CPU usage through top command of pprof, and we can generate pdf or svg by using command pdf or svg.

pdf:

![go-tool-pprof-pdf](https://raywangblog.files.wordpress.com/2017/07/go-tool-pprof-pdf.png?w=1100)

svg:

![go-tool-pprof-svg-0](https://raywangblog.files.wordpress.com/2017/07/go-tool-pprof-svg-0.png?w=1100)

![go-tool-pprof-svg](https://raywangblog.files.wordpress.com/2017/07/go-tool-pprof-svg.png?w=1100)

Request the address below to get go routines information:
```
http://localhost:8081/debug/pprof/goroutine?debug=1
```

![web-show-pprof-goroutine-debug](https://raywangblog.files.wordpress.com/2017/07/web-show-pprof-goroutine-debug.png?w=1100)

Except for these simple commands, you can go deeper. You can list the CPU time consuming of each function or by search specific function to view:

![pprof-list](https://raywangblog.files.wordpress.com/2017/07/pprof-list.png?w=1100)

Find specific function and view the CPU time to consume. Here we search the function main.findMaxCrossingSubArray shows in the svg diagram:

![svg-find-the-max-subarray](https://raywangblog.files.wordpress.com/2017/07/svg-find-the-max-subarray.png?w=1100)

![pprof-list-find](https://raywangblog.files.wordpress.com/2017/07/pprof-list-find.png?w=1100)

And show heap allocation and GC information:

![pprof-heap](https://raywangblog.files.wordpress.com/2017/07/pprof-heap.png?w=1100)

The above methods of go profiling are not very human-friendly, there’s a uber’s go-torch can show the graphic information of time-consuming of each function:

Use below command to generate profile info:

![go-torch-seconds-httpaddr](https://raywangblog.files.wordpress.com/2017/07/go-torch-seconds-httpaddr.png?w=1100)

Once the profile information collected, it will generate as SVG file:

![uber-torch-flame-graph-svg](https://raywangblog.files.wordpress.com/2017/07/uber-torch-flame-graph-svg.png?w=1100)

Open the SVG file:

![uber-torch-flame-graph-svg](https://raywangblog.files.wordpress.com/2017/07/uber-torch-flame-graph-svg.png?w=1100)

![uber-torch-flame-graph-svg-performance-grapth](https://raywangblog.files.wordpress.com/2017/07/uber-torch-flame-graph-svg-performance-grapth.png?w=1100)

The x bar width longer, the time the function consumes more.

![svg-flame-graph-go-torch-small](https://raywangblog.files.wordpress.com/2017/07/svg-flame-graph-go-torch-small.png?w=1100)

P.S: If you use custom routers, you need register those default RESTful API yourself:

{% highlight go %}
func main() {
    r := http.NewServeMux()
    r.HandleFunc("/", hiHandler)
    // Register pprof handlers
    r.HandleFunc("/debug/pprof/", pprof.Index)
    r.HandleFunc("/debug/pprof/cmdline", pprof.Cmdline)
    r.HandleFunc("/debug/pprof/profile", pprof.Profile)
    r.HandleFunc("/debug/pprof/symbol", pprof.Symbol)
    r.HandleFunc("/debug/pprof/trace", pprof.Trace)

    http.ListenAndServe(":8080", r)
}
{% endhighlight %}

Or you just add the code block like below to serve on another port:
{% highlight go %}
go func() {
    http.ListenAndServe(":8080", nil)
}()
{% endhighlight %}

# Conclusion

I now know there are tools can tune your go code performance, especially the standard libraries the go language provides. We should improve our code performance use these tools out there day in day out, to find out what code block consume much CPU or memory. By using those tools the go language and thirty libs provide us can make us more professional and do our work  efficiently.

# Reference

[https://blog.golang.org/profiling-go-programs](https://blog.golang.org/profiling-go-programs)

[https://software.intel.com/en-us/blogs/2014/05/10/debugging-performance-issues-in-go-programs](https://software.intel.com/en-us/blogs/2014/05/10/debugging-performance-issues-in-go-programs)

[https://medium.com/@hackintoshrao/daily-code-optimization-using-benchmarks-and-profiling-in-golang-gophercon-india-2016-talk-874c8b4dc3c5](https://medium.com/@hackintoshrao/daily-code-optimization-using-benchmarks-and-profiling-in-golang-gophercon-india-2016-talk-874c8b4dc3c5)

[http://artem.krylysov.com/blog/2017/03/13/profiling-and-optimizing-go-web-applications/](http://artem.krylysov.com/blog/2017/03/13/profiling-and-optimizing-go-web-applications/)