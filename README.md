# Introduction

Back in 2000 Joel Spolsky wrote _The Guerrilla Guide to Interviewing_ ...

* https://www.cse.unr.edu/~sushil/researchAdvice/GIV1.html

... and provided these programming questions:

1. Reverse a string in place
2. Reverse a linked list
3. Count all the bits that are on in a byte
4. Binary search
5. Find the longest run in a string
6. atoi
7. itoa (great, because they have to use a stack or strrev)

Here are my answers.

All programs can be run in the [Online C++ compiler](https://www.onlinegdb.com/online_c++_compiler)

# 1. Reverse String in Place

```c
#include <stdio.h>
#include <string.h>

void reverse_string_in_place( char *text )
{
    if (!text) return;
    size_t len = strlen( text );
    char  *beg = text;
    char  *end = text + len - 1;
    char   tmp;

    while (beg < end)
    {
//printf( "%c <-> %c\n", *beg, *end ); // DEBUG
        tmp = *beg;
              *beg = *end;
                     *end = tmp;
        beg++;
        end--;
    }

}

int main()
{
    char text[] = { "Reverse me!" };

    printf( "Before: %s\n", text );
    reverse_string_in_place( text );
    printf( "After : %s\n", text );

    return 0;
}
```

# 2. Reverse a linked list

WIP.

# 3. Count all the bits that are on in a byte

There are many solutions to this problem.  Modern CPUs have a native `popcnt` instruction, such as x86 `_mm_popcnt_u32()`, and many C/C++ compilers provide an intrinsic `__builtin__popcount()` so using an array table can be hideously slow (causing a cache miss) depending on the architecture.

See: _Built Twiddling Hacks_ and this SO question:

* https://stackoverflow.com/questions/51387998/count-bits-1-on-an-integer-as-fast-as-gcc-builtin-popcountint

Counting bits in a byte is rather trivial but for int32 and int64 a common gotcha to look out for is:

* Does your algorithm handle negative integers?

A proper solution would use `uint8_t`, `uint16_t`, `uint32_t`, and `uint64_` to minimize ambuigity.

There are various ways to **pretty print** the output

Simple:

```c
    for( int byte = 0; byte < 256; ++byte )
    {
        printf( "%02X: %d    ", byte, countbits8( byte ) );
        if ((byte & 7) == 7) printf( "\n" );
    }
```

Which produces:

```
00: 0    01: 1    02: 1    03: 2    04: 1    05: 2    06: 2    07: 3    
08: 1    09: 2    0A: 2    0B: 3    0C: 2    0D: 3    0E: 3    0F: 4    
10: 1    11: 2    12: 2    13: 3    14: 2    15: 3    16: 3    17: 4    
18: 2    19: 3    1A: 3    1B: 4    1C: 3    1D: 4    1E: 4    1F: 5    
20: 1    21: 2    22: 2    23: 3    24: 2    25: 3    26: 3    27: 4    
28: 2    29: 3    2A: 3    2B: 4    2C: 3    2D: 4    2E: 4    2F: 5    
30: 2    31: 3    32: 3    33: 4    34: 3    35: 4    36: 4    37: 5    
38: 3    39: 4    3A: 4    3B: 5    3C: 4    3D: 5    3E: 5    3F: 6    
40: 1    41: 2    42: 2    43: 3    44: 2    45: 3    46: 3    47: 4    
48: 2    49: 3    4A: 3    4B: 4    4C: 3    4D: 4    4E: 4    4F: 5    
50: 2    51: 3    52: 3    53: 4    54: 3    55: 4    56: 4    57: 5    
58: 3    59: 4    5A: 4    5B: 5    5C: 4    5D: 5    5E: 5    5F: 6    
60: 2    61: 3    62: 3    63: 4    64: 3    65: 4    66: 4    67: 5    
68: 3    69: 4    6A: 4    6B: 5    6C: 4    6D: 5    6E: 5    6F: 6    
70: 3    71: 4    72: 4    73: 5    74: 4    75: 5    76: 5    77: 6    
78: 4    79: 5    7A: 5    7B: 6    7C: 5    7D: 6    7E: 6    7F: 7    
80: 1    81: 2    82: 2    83: 3    84: 2    85: 3    86: 3    87: 4    
88: 2    89: 3    8A: 3    8B: 4    8C: 3    8D: 4    8E: 4    8F: 5    
90: 2    91: 3    92: 3    93: 4    94: 3    95: 4    96: 4    97: 5    
98: 3    99: 4    9A: 4    9B: 5    9C: 4    9D: 5    9E: 5    9F: 6    
A0: 2    A1: 3    A2: 3    A3: 4    A4: 3    A5: 4    A6: 4    A7: 5    
A8: 3    A9: 4    AA: 4    AB: 5    AC: 4    AD: 5    AE: 5    AF: 6    
B0: 3    B1: 4    B2: 4    B3: 5    B4: 4    B5: 5    B6: 5    B7: 6    
B8: 4    B9: 5    BA: 5    BB: 6    BC: 5    BD: 6    BE: 6    BF: 7    
C0: 2    C1: 3    C2: 3    C3: 4    C4: 3    C5: 4    C6: 4    C7: 5    
C8: 3    C9: 4    CA: 4    CB: 5    CC: 4    CD: 5    CE: 5    CF: 6    
D0: 3    D1: 4    D2: 4    D3: 5    D4: 4    D5: 5    D6: 5    D7: 6    
D8: 4    D9: 5    DA: 5    DB: 6    DC: 5    DD: 6    DE: 6    DF: 7    
E0: 3    E1: 4    E2: 4    E3: 5    E4: 4    E5: 5    E6: 5    E7: 6    
E8: 4    E9: 5    EA: 5    EB: 6    EC: 5    ED: 6    EE: 6    EF: 7    
F0: 4    F1: 5    F2: 5    F3: 6    F4: 5    F5: 6    F6: 6    F7: 7    
F8: 5    F9: 6    FA: 6    FB: 7    FC: 6    FD: 7    FE: 7    FF: 8 
```
We can output a C table with a little bit of code cleanup:

```c
    const int NUM_COLUMNS = 8;

    printf( "{\n" );
    for( int byte = 0; byte < 256; byte += NUM_COLUMNS )
    {
        printf( "    " );
        for( int col = 0; col < NUM_COLUMNS; ++col )
            printf( "%d, ", countbits8( byte + col ) );
        printf( "  // " );
        for( int col = 0; col < NUM_COLUMNS; ++col )
            printf( "%02X ", byte + col );
        printf( "\n" );
    }
    printf( "};\n" );
```

With 8 columns our fancy table produces:

```
{
    0, 1, 1, 2, 1, 2, 2, 3,   // 00 01 02 03 04 05 06 07 
    1, 2, 2, 3, 2, 3, 3, 4,   // 08 09 0A 0B 0C 0D 0E 0F 
    1, 2, 2, 3, 2, 3, 3, 4,   // 10 11 12 13 14 15 16 17 
    2, 3, 3, 4, 3, 4, 4, 5,   // 18 19 1A 1B 1C 1D 1E 1F 
    1, 2, 2, 3, 2, 3, 3, 4,   // 20 21 22 23 24 25 26 27 
    2, 3, 3, 4, 3, 4, 4, 5,   // 28 29 2A 2B 2C 2D 2E 2F 
    2, 3, 3, 4, 3, 4, 4, 5,   // 30 31 32 33 34 35 36 37 
    3, 4, 4, 5, 4, 5, 5, 6,   // 38 39 3A 3B 3C 3D 3E 3F 
    1, 2, 2, 3, 2, 3, 3, 4,   // 40 41 42 43 44 45 46 47 
    2, 3, 3, 4, 3, 4, 4, 5,   // 48 49 4A 4B 4C 4D 4E 4F 
    2, 3, 3, 4, 3, 4, 4, 5,   // 50 51 52 53 54 55 56 57 
    3, 4, 4, 5, 4, 5, 5, 6,   // 58 59 5A 5B 5C 5D 5E 5F 
    2, 3, 3, 4, 3, 4, 4, 5,   // 60 61 62 63 64 65 66 67 
    3, 4, 4, 5, 4, 5, 5, 6,   // 68 69 6A 6B 6C 6D 6E 6F 
    3, 4, 4, 5, 4, 5, 5, 6,   // 70 71 72 73 74 75 76 77 
    4, 5, 5, 6, 5, 6, 6, 7,   // 78 79 7A 7B 7C 7D 7E 7F 
    1, 2, 2, 3, 2, 3, 3, 4,   // 80 81 82 83 84 85 86 87 
    2, 3, 3, 4, 3, 4, 4, 5,   // 88 89 8A 8B 8C 8D 8E 8F 
    2, 3, 3, 4, 3, 4, 4, 5,   // 90 91 92 93 94 95 96 97 
    3, 4, 4, 5, 4, 5, 5, 6,   // 98 99 9A 9B 9C 9D 9E 9F 
    2, 3, 3, 4, 3, 4, 4, 5,   // A0 A1 A2 A3 A4 A5 A6 A7 
    3, 4, 4, 5, 4, 5, 5, 6,   // A8 A9 AA AB AC AD AE AF 
    3, 4, 4, 5, 4, 5, 5, 6,   // B0 B1 B2 B3 B4 B5 B6 B7 
    4, 5, 5, 6, 5, 6, 6, 7,   // B8 B9 BA BB BC BD BE BF 
    2, 3, 3, 4, 3, 4, 4, 5,   // C0 C1 C2 C3 C4 C5 C6 C7 
    3, 4, 4, 5, 4, 5, 5, 6,   // C8 C9 CA CB CC CD CE CF 
    3, 4, 4, 5, 4, 5, 5, 6,   // D0 D1 D2 D3 D4 D5 D6 D7 
    4, 5, 5, 6, 5, 6, 6, 7,   // D8 D9 DA DB DC DD DE DF 
    3, 4, 4, 5, 4, 5, 5, 6,   // E0 E1 E2 E3 E4 E5 E6 E7 
    4, 5, 5, 6, 5, 6, 6, 7,   // E8 E9 EA EB EC ED EE EF 
    4, 5, 5, 6, 5, 6, 6, 7,   // F0 F1 F2 F3 F4 F5 F6 F7 
    5, 6, 6, 7, 6, 7, 7, 8,   // F8 F9 FA FB FC FD FE FF 
};
```

Changing columns to 16 our fancy table16 produces this:

```
{
    0, 1, 1, 2, 1, 2, 2, 3, 1, 2, 2, 3, 2, 3, 3, 4,   // 00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F 
    1, 2, 2, 3, 2, 3, 3, 4, 2, 3, 3, 4, 3, 4, 4, 5,   // 10 11 12 13 14 15 16 17 18 19 1A 1B 1C 1D 1E 1F 
    1, 2, 2, 3, 2, 3, 3, 4, 2, 3, 3, 4, 3, 4, 4, 5,   // 20 21 22 23 24 25 26 27 28 29 2A 2B 2C 2D 2E 2F 
    2, 3, 3, 4, 3, 4, 4, 5, 3, 4, 4, 5, 4, 5, 5, 6,   // 30 31 32 33 34 35 36 37 38 39 3A 3B 3C 3D 3E 3F 
    1, 2, 2, 3, 2, 3, 3, 4, 2, 3, 3, 4, 3, 4, 4, 5,   // 40 41 42 43 44 45 46 47 48 49 4A 4B 4C 4D 4E 4F 
    2, 3, 3, 4, 3, 4, 4, 5, 3, 4, 4, 5, 4, 5, 5, 6,   // 50 51 52 53 54 55 56 57 58 59 5A 5B 5C 5D 5E 5F 
    2, 3, 3, 4, 3, 4, 4, 5, 3, 4, 4, 5, 4, 5, 5, 6,   // 60 61 62 63 64 65 66 67 68 69 6A 6B 6C 6D 6E 6F 
    3, 4, 4, 5, 4, 5, 5, 6, 4, 5, 5, 6, 5, 6, 6, 7,   // 70 71 72 73 74 75 76 77 78 79 7A 7B 7C 7D 7E 7F 
    1, 2, 2, 3, 2, 3, 3, 4, 2, 3, 3, 4, 3, 4, 4, 5,   // 80 81 82 83 84 85 86 87 88 89 8A 8B 8C 8D 8E 8F 
    2, 3, 3, 4, 3, 4, 4, 5, 3, 4, 4, 5, 4, 5, 5, 6,   // 90 91 92 93 94 95 96 97 98 99 9A 9B 9C 9D 9E 9F 
    2, 3, 3, 4, 3, 4, 4, 5, 3, 4, 4, 5, 4, 5, 5, 6,   // A0 A1 A2 A3 A4 A5 A6 A7 A8 A9 AA AB AC AD AE AF 
    3, 4, 4, 5, 4, 5, 5, 6, 4, 5, 5, 6, 5, 6, 6, 7,   // B0 B1 B2 B3 B4 B5 B6 B7 B8 B9 BA BB BC BD BE BF 
    2, 3, 3, 4, 3, 4, 4, 5, 3, 4, 4, 5, 4, 5, 5, 6,   // C0 C1 C2 C3 C4 C5 C6 C7 C8 C9 CA CB CC CD CE CF 
    3, 4, 4, 5, 4, 5, 5, 6, 4, 5, 5, 6, 5, 6, 6, 7,   // D0 D1 D2 D3 D4 D5 D6 D7 D8 D9 DA DB DC DD DE DF 
    3, 4, 4, 5, 4, 5, 5, 6, 4, 5, 5, 6, 5, 6, 6, 7,   // E0 E1 E2 E3 E4 E5 E6 E7 E8 E9 EA EB EC ED EE EF 
    4, 5, 5, 6, 5, 6, 6, 7, 5, 6, 6, 7, 6, 7, 7, 8,   // F0 F1 F2 F3 F4 F5 F6 F7 F8 F9 FA FB FC FD FE FF 
};
```

Naive solution:

```c
#include <stdio.h>

int countbits8( int byte )
{
    int total = 0;

    for( int bit = 0; bit < 8; ++bit, byte >>= 1 )
        total += (byte & 1);

    return total;
}

int main()
{
    const int NUM_COLUMNS = 8;

    printf( "{\n" );
    for( int byte = 0; byte < 256; byte += NUM_COLUMNS )
    {
        printf( "    " );
        for( int col = 0; col < NUM_COLUMNS; ++col )
            printf( "%d, ", countbits8( byte + col ) );
        printf( "  // " );
        for( int col = 0; col < NUM_COLUMNS; ++col )
            printf( "%02X ", byte + col );
        printf( "\n" );
    }
    printf( "};\n" );
}
```

Array 4 solution:

```c
#include <stdio.h>

int countbits8( int byte )
{
    int total = 0;
    const int TWO_BITS[4] = { 0, 1, 1, 2 }; // 0000, 0001, 0010, 0011

    for( int bit = 0; bit < 8; bit += 2, byte >>= 2 )
        total += TWO_BITS[ byte & 0x3 ];

    return total;
}

int main()
{
    for( int byte = 0; byte < 256; ++byte )
    {
        printf( "%02X: %d    ", byte, countbits8( byte ) );
        if ((byte & 7) == 7) printf( "\n" );
    }
}
```

Array 16 solution:

```c
#include <stdio.h>

const char NIBBLE_BITS_ON[16] =
{
      0 // 0  0000
    , 1 // 1  0001
    , 1 // 2  0010
    , 2 // 3  0011

    , 1 // 4  0100  // 0 +1
    , 2 // 5  0101  // 1 +1
    , 2 // 6  0110  // 1 +1
    , 3 // 7  0111  // 2 +1

    , 1 // 8  1000  // 0 +1
    , 2 // 9  1001  // 1 +1
    , 2 // A  1010  // 1 +1
    , 3 // B  1011  // 2 +1

    , 2 // C  1100  // 0 +2
    , 3 // D  1101  // 1 +2
    , 3 // E  1110  // 1 +2
    , 4 // F  1111  // 2 +2 
};

int countbits8( int byte )
{
    int total = NIBBLE_BITS_ON[ (byte >> 4) & 0xF ]
              + NIBBLE_BITS_ON[ (byte >> 0) & 0xF ];
    return total;
}

int main()
{
    for( int byte = 0; byte < 256; ++byte )
    {
        printf( "%02X: %d    ", byte, countbits8( byte ) );
        if ((byte & 7) == 7) printf( "\n" );
    }
}
```

Array 256 with memoization solution:

```c
#include <stdio.h>

int countbits8( int byte )
{
    const int   COUNT_JUNK = -1;  // not initialized
    const int   COUNT_SIZE = 256;
    static int  COUNT_BITS[ COUNT_SIZE ];
    static bool count_init = false;

    if (!count_init)
    {
        count_init = true;
        for( int byte = 0; byte < COUNT_SIZE; ++byte )
            COUNT_BITS[ byte ] = COUNT_JUNK;
    }

    int total = COUNT_BITS[ byte & (COUNT_SIZE-1) ];
    if (total <= COUNT_JUNK)
    {
        total = 0;
        const int TWO_BITS[4] = { 0, 1, 1, 2 }; // 0000, 0001, 0010, 0011
        for( int bit = 0; bit < 8; bit += 2, byte >>= 2 )
            total += TWO_BITS[ byte & 0x3 ];
        COUNT_BITS[ byte & 0xFF ] = total;
    }

    return total;
}

int main()
{
    for( int byte = 0; byte < 256; ++byte )
    {
        printf( "%02X: %d    ", byte, countbits8( byte ) );
        if ((byte & 7) == 7) printf( "\n" );
    }
}
```

# 4. Binary search

There are [NINE different types](https://github.com/Michaelangel007/advanced_binary_search) of binary search one can do.  The classical binary search is an _exact_ match of key/value.

Apparently only [10% of programmers](https://reprog.wordpress.com/2010/04/19/are-you-one-of-the-10-percent/) can write a Binary Search from scratch and it took almost **20 years** before a version was published without bugs!

> I was amazed: given ample time, only about ten percent of professional programmers were able to get this small program right.  But they aren’t the only ones to find this task difficult: in the history in Section 6.2.1 of his Sorting and Searching, Knuth points out that while the first binary search was published in 1946, the first published binary search without bugs did not appear until 1962.
> — Jon Bentley, Programming Pearls (1st edition), pp. 35-36.

One gotcha is does your [midpoint calculation](https://ai.googleblog.com/2006/06/extra-extra-read-all-about-it-nearly.html) have sign overflow if the array size > 2^30 ? That is, `mid = (low + high) / 2` has a bug. The correct version is `mid = low + ((high - low)/2)`.

This solution uses **signed** array offsets.  It is left as an exercise for the reader to use unsigned offsets.

And for the love of readability please DON'T use lowecase `l` and `u` because you are too lazy to type "lower" and "upper" for variable names.  (I use `min`, `mid`, and `max` instead for readable array offset names.)

Lastly, please don't return magic numbers such as -1.  There is a reason _const variables_ were invented.

```c
#include <stdio.h>

const int KEY_NOT_FOUND = -1;

// ==================================================
int binary_search( const int key, const int size, const int data[] )
{
    int min = 0;
    int max = size - 1;

    while (min <= max)
    {
        int mid = min + ((max - min) / 2);
        /**/ if (data[mid] < key)   min  = mid + 1;
        else if (data[mid] > key)   max  = mid - 1;
        else /* (data[mid] = key)*/ return mid;
    }

    return KEY_NOT_FOUND;
}

int main()
{
    const int data[] = { 1, 3, 5, 7, 9, 11 };
    const int SIZE   = sizeof(data) / sizeof(data[0]);

    printf( "Array size = [%d]\n", SIZE );
    for( int key = 0; key < 12; ++key )
    {
        int offset = binary_search( key, SIZE, data );

        printf( "Searching %d, ", key );
        if (offset == KEY_NOT_FOUND)
            printf( "not found\n" );
        else
            printf( "found @ [%d]\n", offset );
    }
    printf( "Done\n" );
}
```


# 5. Find the longest run in a string

Find the long run of characters in a string is slightly ambigious.

Is the run consecutive?

* Yes?
* No?

How does your algorithm handle:

* null string?
* trivial case of only 1 letter?
* ties?

Avoid this typical [overengineered garbage code](https://stackoverflow.com/questions/31840381/find-the-longest-sequence-of-same-characters-in-a-string/44536741).

```c
#include <stdio.h>

int longest_run_with_consecutive( const char *text, char *found )
{
    if (!text) return 0;

          int   len   = 0;        // current run
          char  max   = 0;        // val
          char  which = text[0];  // key, which char that has largest run
          char  last  = text[0];
    const char *head  = text;

    for( ; *head; head++ )
    {
        if (*head != last)
        {
            if (max < len)
            {
                max   = len ; // val
                which = last; // key
            }
            last = *head;
            len  = 1;  // reset current run
        }
        else
            len++;
    }

    if (!max)   max   = len  ;  // val: Handle trivial case of only 1 letter
    if (found) *found = which;  // key: Does caller want to know which character had longest run?

    return max;
}

int longest_run_sans_consecutive( const char *text, char *found )
{
    if (!text) return 0;

    const int COUNT_SIZE = 256;  // 8-bit ASCII
          int total[ COUNT_SIZE ];

    for( int i = 0; i < COUNT_SIZE; ++i )
        total[ i ] = 0;

    const char *head = text;
    for( ; *head; head++ )
        total[ *head ]++;

    int max = total[0];
    char which;

    for( int i = 1; i < COUNT_SIZE; ++i )
        if (max < total[ i ])
        {
            max   = total[ i ];
            which = i;
        }

    if (found) *found = which;
    return max;
}

void demo( const char *text )
{
    int  total;
    char which;

    printf( "Searching: '%s'\n", text );
    total = longest_run_with_consecutive( text, &which ); printf( "  Longest with consecutive: %d @ %c\n", total, which );
    total = longest_run_sans_consecutive( text, &which ); printf( "  Longest sans consecutive: %d @ %c\n", total, which );

    printf( "\n" );
}


int main()
{
    int total;
    const char *text1 = "aabccedeeeeef"; // consecutive: 5 = e, nonconsecutive: 6 = e
    const char *text2 = "aaackccccccaz"; // consecutive: 6 = c, nonconsecutive: 7 = c

    demo( text1 );
    demo( text2 );
    demo( "aaa" );

    return 0;
}
```

# 6. atoi()

```c
#include <stdio.h>  // printf()
#include <stdlib.h> // atoi()

// [+][0-9]...[0-9]
// [-][0-9]...[0-9]
int my_atoi( const char *text )
{
    const int  BASE = 10;
          int  n = 0;
          bool is_neg = false;

    if (!text)          return n;
    if (*text == '+') { text++;                } // optional '+' does NOT change sign
    if (*text == '-') { text++; is_neg = true; } // optional '-' DOES change sign

    while (*text)
    {
        char c = *text++;

        if ((c < '0') || (c > '9')) break; // isdigit()
        n *= BASE;
        n += (c - '0');
    }

    if (is_neg)
        n = -n;

    return n;
}

int main()
{
    int n;

    n = atoi( "+123" ); printf( "= %d\n", n ); // 123
    n = atoi(  "123" ); printf( "= %d\n", n ); // 123
    n = atoi( "-123" ); printf( "= %d\n", n ); // -123
    n = atoi(     "" ); printf( "= %d\n", n ); // 0
    n = atoi(    "-" ); printf( "= %d\n", n ); // 0
    n = atoi(   "-0" ); printf( "= %d\n", n ); // 0

    n = my_atoi( "+123" ); printf( "= %d\n", n ); // 123
    n = my_atoi(  "123" ); printf( "= %d\n", n ); // 123
    n = my_atoi( "-123" ); printf( "= %d\n", n ); // -123
    n = my_atoi(     "" ); printf( "= %d\n", n ); // 0
    n = my_atoi(    "-" ); printf( "= %d\n", n ); // 0
    n = my_atoi(   "-0" ); printf( "= %d\n", n ); // 0

    for( int i = 0; i < (1 << 31); ++i )
    {
        char text[16];
        sprintf( text, "%d", i );

        int actual = my_atoi( text );
        int expect =    atoi( text );
        if (expect != actual)
            return printf( "ERROR: '%s': %d != %d\n", text, actual, expect );
    }
    printf( "passed\n" );

    return 0;
}

```

For full robustness, such as handling  multiple signs, we could use a state machine ...

```
    enum STATE =
    {
         STATE_NO_SIGN
        ,STATE_POS_SIGN
        ,STATE_NEG_SIGN
        ,STATE_DIGIT
        ,STATE_END
    };

```

... but that is left as an exercise for the reader.


# 7. itoa()

```c
#include <stdio.h>

// Related:
//   printf( "%d",  )
//   sprintf( buffer, "%d", int_var )
// char* itoa( int n, char *buffer, int radix ); // stdlib.h
// NOTE: NOT Thread-safe
char* my_itoa( int n )
{
    bool is_neg = (n < 0);
    int  x      = is_neg
                ? -n
                :  n
                ;

    // Only need buffer[11] since 2^32 = 10 digits + null
    //     2^32 = 4294967296
    //           [0123456789A]
    static char  buffer[16];
           char *head = buffer + 15;
    *head-- = 0;

    const int BASE = 10;
    while (x >= BASE)
    {
        *head-- = (x % 10) + '0';
                  x /= BASE;
    }

    // always do at least one digit; don't need to do final / BASE
    *head-- = (x % 10) + '0';

    if (is_neg)
        *head-- = '-';

    return head+1;
}

int main()
{
    printf( "%s\n", itoa( 123 ) );
    printf( "%s\n", itoa(-123 ) );

    for( int i = 0; i < 1001; ++i )
    {
        printf( "%s   ", my_itoa( i ) );
        if ((i % 10) == 9) printf( "\n" );
    }
    printf( "\n" );

    for( int i = 0; i > -1001; --i )
    {
        printf( "%s   ", my_itoa( i ) );
        if ((-i % 10) == 9) printf( "\n" );
    }
    printf( "\n" );
}
```
