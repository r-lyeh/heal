heal
====

- Heal is a lightweight C++ framework to aid and debug applications. Requires C++11.
- Heal requires OS dependencies only.
- Heal has full API support for Windows. Feel free to tweak heal.cpp and add more OSes :)
- MIT licensed.

API
---

- `assert1( a == b )`
- `assert2( a == b, "a must be equal to b" )`
- `assert3( a, ==, b )`
- `assert4( a, ==, b, "a must be equal to b" )`. Any assertion fail will invoke `cb::custom_assert_cb()` callback.
- `setup_assert(cb)` @todocument.
- `bool debugger(string reason)` tries to invoke debugger, if possible.
- `void breakpoint()` breaks execution, if possible.
- `string demangle(string symbol)` returns an human-readable mangled-symbol, if possible.
- `errorbox(string body,string title)` shows an error window.
- `bool is_asserting()` determines if assertions are enabled or not.
- `bool is_release()` returns true if either `NDEBUG` or `_NDEBUG` preprocessor directives are defined.
- `bool is_debug()` returns negation of `is_release()`
- `callstack()` @todocument.
- `stacktrace()` returns a vector of strings that contains full current callstack. Output can be formatted. Possible output for `std::cout << stacktrace()`:
<pre>
#0 stacktrace (heal.cpp, line 592)
#1 main (sample.cc, line 14)
#2 __tmainCRTStartup (f:\dd\vctools\crt_bld\self_x86\crt\src\crt0.c, line 240)
#3 BaseThreadInitThunk
#4 RtlInitializeExceptionChain
#5 RtlInitializeExceptionChain
</pre>
- `hexdump()` returns an std::string hexdump of given memory pointers. Possible output for `std::cout << hexdump(memory)`:
<pre>
offset   00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F [ptr=0014F844 sz=10]
0014F844  H  e  l  l  o  W  o  r  l  d  .  .  .  .  .  . asc
0014F844 48 65 6c 6c 6f 57 6f 72 6c 64 ?? ?? ?? ?? ?? ?? hex
</pre>

Sample
------
<pre>
#include &lt;iostream&gt;
#include "heal.hpp"

int main()
{
    if( !is_asserting() )
        errorbox( "Asserts are disabled. No assertions will be perfomed" );

    assert3( 1, &lt;, 2);
    assert4( 1, &lt;, 2, "This should never happen" );

    std::cout &lt;&lt; hexdump(3.14159f) &lt;&lt; std::endl;
    std::cout &lt;&lt; hexdump("hello world") &lt;&lt; std::endl;

    std::cout &lt;&lt; ( is_debug() ? "Debug build" : "Release build" ) &lt;&lt; std::endl;
    std::cout &lt;&lt; stacktrace("\1) \2\n") &lt;&lt; std::endl;

    if( !debugger("We are about to launch debugger...") )
        die( "Something went wrong: debugger() didnt work" );

    return 0;
}
</pre>

Possible output
---------------
<pre>
D:\prj\heal>cl sample.cc heal.cpp /Zi
D:\prj\heal>sample.exe
offset   00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F [ptr=0032F69C sz=4]
0032F69C  .  .  I  @  .  .  .  .  .  .  .  .  .  .  .  . asc
0032F69C d0 0f 49 40 ?? ?? ?? ?? ?? ?? ?? ?? ?? ?? ?? ?? hex

offset   00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F [ptr=0032F4F4 sz=11]
0032F4F4  h  e  l  l  o     w  o  r  l  d  .  .  .  .  . asc
0032F4F4 68 65 6c 6c 6f 20 77 6f 72 6c 64 ?? ?? ?? ?? ?? hex

Debug build
0) stacktrace (d:\prj\heal\heal.cpp, line 577)
1) main (d:\prj\heal\sample.cc, line 16)
2) __tmainCRTStartup (f:\dd\vctools\crt_bld\self_x86\crt\src\crt0.c, line 240)
3) BaseThreadInitThunk
4) RtlInitializeExceptionChain
5) RtlInitializeExceptionChain

Something went wrong: debugger() didnt work
#0 stacktrace (d:\prj\heal\heal.cpp, line 577)
#1 die (d:\prj\heal\heal.cpp, line 586)
#2 main (d:\prj\heal\sample.cc, line 19)
#3 __tmainCRTStartup (f:\dd\vctools\crt_bld\self_x86\crt\src\crt0.c, line 240)
#4 BaseThreadInitThunk
#5 RtlInitializeExceptionChain
#6 RtlInitializeExceptionChain
D:\prj\heal>
</pre>
