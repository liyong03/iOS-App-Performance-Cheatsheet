# Foundation

- [NSDateFormatter](#nsdateformatter)
- [NSFileManager](#nsfilemanager)
- [NSObjCRuntime](#nsobjcruntime)
- [NSString](#nsstring)

---

### NSDateFormatter

`NSDateFormatter` 不是唯一一个创建的开销就很昂贵的类，但是它却是常用的、开销大到 Apple 会特别建议应该缓存和重复使用实例的一个。

> 创建一个 Date Formatter 不是一个廉价操作。如果你可能会频繁使用一个 formatter，那么创建缓存一个单例比重复创建销毁多个实例要高效很多。一种办法就是使用静态变量。

[Source](https://developer.apple.com/library/ios/documentation/cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html)

一种通用的缓存 `NSDateFormatter` 的方法是使用 `-[NSThread threadDictionary]`（因为 `NSDateFormatter` 不是线程安全的）：

```objective-c
+ (NSDateFormatter *)cachedDateFormatter {
	NSMutableDictionary *threadDictionary = [[NSThread currentThread] threadDictionary];
	NSDateFormatter *dateFormatter = [threadDictionary objectForKey:@"cachedDateFormatter"];
    if (dateFormatter == nil) {
        dateFormatter = [[NSDateFormatter alloc] init];
        [dateFormatter setLocale:[NSLocale currentLocale]];
        [dateFormatter setDateFormat: @"YYYY-MM-dd HH:mm:ss"];
        [threadDictionary setObject:dateFormatter forKey:@"cachedDateFormatter"];
    }
    return dateFormatter;
}
```

#####- (NSDate *)dateFromString:(NSString *)string

这可能是最常见的 iOS 性能瓶颈。经过多方努力寻找，下面是 [ISO8601](http://en.wikipedia.org/wiki/ISO_8601) 转成 `NSDate` 的 `NSDateFormatter` 的最著名替代品。

######strptime 

```objective-c
//#include <time.h>

time_t t;
struct tm tm;
strptime([iso8601String cStringUsingEncoding:NSUTF8StringEncoding], "%Y-%m-%dT%H:%M:%S%z", &tm);
tm.tm_isdst = -1;
t = mktime(&tm);
[NSDate dateWithTimeIntervalSince1970:t + [[NSTimeZone localTimeZone] secondsFromGMT]];
```

[Source](http://sam.roon.io/how-to-drastically-improve-your-app-with-an-afternoon-and-instruments)

######sqlite3

```objective-c
//#import "sqlite3.h"

sqlite3 *db = NULL;
sqlite3_open(":memory:", &db);
sqlite3_stmt *statement = NULL;
sqlite3_prepare_v2(db, "SELECT strftime('%s', ?);", -1, &statement, NULL);
sqlite3_bind_text(statement, 1, [iso8601String UTF8String], -1, SQLITE_STATIC);
sqlite3_step(statement);
int64_t value = sqlite3_column_int64(statement, 0);
NSDate *date = [NSDate dateWithTimeIntervalSince1970:value];
sqlite3_clear_bindings(statement);
sqlite3_reset(statement);
```

[Source](http://vombat.tumblr.com/post/60530544401/date-parsing-performance-on-ios-nsdateformatter-vs)


### NSFileManager

#####- (NSDictionary *)attributesOfItemAtPath:(NSString *)filePath error:(NSError *)error

当试图获取磁盘中一个文件的属性信息时，使用 `–[NSFileManager attributesOfItemAtPath:error:]` 会浪费大量时间读取你可能根本不需要的附加属性。这时你可以使用 `stat` 代替 `NSFileManager`，直接获取文件属性：

```objective-c
//#import <sys/stat.h>

struct stat statbuf;
const char *cpath = [filePath fileSystemRepresentation];
if (cpath && stat(cpath, &statbuf) == 0) {
    NSNumber *fileSize = [NSNumber numberWithUnsignedLongLong:statbuf.st_size];
    NSDate *modificationDate = [NSDate dateWithTimeIntervalSince1970:statbuf.st_mtime];
    NSDate *creationDate = [NSDate dateWithTimeIntervalSince1970:statbuf.st_ctime];
    // etc
}
```

### NSObjCRuntime

#####NSLog(NSString *format, ...)

`NSLog()` 写消息到 Apple 的系统日志。当通过 Xcode 变异运行程序时，被写出的日志会展现在调试终端，同事也会写到设备产品终端日志中。此外，系统会在主线程序列化 `NSLog()` 的内容。即使是最新的 iOS 设备，`NSLog()` 输出调试信息所花的时间也是无法忽略的。所以在产品环境中推荐尽可能少的使用 `NSLog()`。

> 调用 `NSLog` 会为每一行日志生成一个事件，应避免过度使用 `NSLog`。

[Source](https://developer.apple.com/videos/wwdc/2012/?id=235)

下面是通常会用到的宏定义，它会根据 debug/production 来选择执行 `NSLog()`：

```objective-c
#ifdef DEBUG
// Only log when attached to the debugger
#    define DLog(...) NSLog(__VA_ARGS__)
#else
#    define DLog(...) /* */
#endif
// Always log, even in production
#define ALog(...) NSLog(__VA_ARGS__)
```

[Source](http://iphoneincubator.com/blog/debugging/the-evolution-of-a-replacement-for-nslog)

### NSString

#####+ (instancetype)stringWithFormat:(NSString *)format,, ...

创建 `NSString` 不是特别昂贵，但是当在紧凑循环（比如作为字典的键值）中使用时， `+[NSString stringWithFormat:]` 的性能可以通过使用类似 `asprintf` 的 C 函数显著提高。

```objective-c
NSString *firstName = @"Daniel";
NSString *lastName = @"Amitay";
char *buffer;
asprintf(&buffer, "Full name: %s %s", [firstName UTF8String], [lastName UTF8String]);
NSString *fullName = [NSString stringWithCString:buffer encoding:NSUTF8StringEncoding];
free(buffer);
```

#####- (instancetype)initWithFormat:(NSString *)format, ...

参考 [`+[NSString stringWithFormat:]`](#-instancetypestringwithformatnsstring-format-)
