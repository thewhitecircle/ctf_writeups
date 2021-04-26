# re

## Baby’s First Reversing

> Solved By : thewhiteh4t

* we have a python bytecode file
* we can de-compile it using `uncompyle6`
* here is the resultant python script

```python
    def __main__(inp):
        i = -4
        for c in inp:
            if i == 4:
                if c != ' ':
                    exit(82)
                else:
                    if i == -4:
                        if c != 'W':
                            exit(133)
                        else:
                            if i == -2:
                                if c != 'I':
                                    exit(42069)
                                elif i == -1 and c != '{':
                                    exit(11037)
                                if i == 10:
                                    if c != '}':
                                        exit(9001)
                            else:
                                if i == 1:
                                    if c != '@':
                                        exit(11037)
                                if i == 2 and c != '5':
                                    exit(11037)
                            if i == 7 and c != 'P':
                                exit(11037)
                        if i == 3:
                            if c != 'E':
                                exit(11037)
                    else:
                        if i == 0:
                            if c != 'h':
                                exit(82)
                        if i == 5 and c != 'h':
                            exit(11037)
                    if i == -3 and c != 'P':
                        exit(133)
                if i == 9:
                    if c != '!':
                        exit(133)
            else:
                if i == 6:
                    if c != '0':
                        exit(133)
                if i == 8 and c != '3':
                    exit(133)
            i += 1
        else:
            print(':)')
    
    __main__(input('hi'))
```

* if we carefully observe we can see a pattern
* it is checking the value of `i` and `c`
* if the condition is not satisfied program exits with an exit code

```python
    if i == -4:
      if c != 'W':
        exit(133)
```

* if we look at the values of i and c we can see another pattern

```bash
    i == -4 -> c = W
    i == -3 -> c = P
    i == -2 -> c = I
    i == -1 -> c = {
    i == 0  -> c = h
    i == 1  -> c = @
    i == 2  -> c = 5
    i == 3  -> c = E
    i == 4  -> c = SPACE
    i == 5  -> c = h
    i == 6  -> c = 0
    i == 7  -> c = P
    i == 8  -> c = 3
    i == 9  -> c = !
    i == 10 -> c = }
```

```
    WPI{h@5E h0P3!}
```