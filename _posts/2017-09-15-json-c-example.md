---
layout: post
title: JSON-C example
---

This example is based on version 0.12, for more information see the [json-c API documentation](http://json-c.github.io/json-c/).

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdbool.h>
#include "json-c/json.h"

void main()
{
    char s[1024] = "{"
      "\"name\": \"ty\","
      "\"age\":25,"
      "\"hobbies\":[\"reading\", \"watch movies\"]"
    "}";

    enum json_tokener_error error;
    json_object *obj = json_tokener_parse_verbose(s, &error);
    if (error != json_tokener_success) {
        printf("Parse error. \n");
        return;
    }

    json_object *value;
    bool isFound = json_object_object_get_ex(obj, "name", &value);
    if (isFound)
        printf("name: %s \n", json_object_get_string(value));

    isFound = json_object_object_get_ex(obj, "age", &value);
    if (isFound)
        printf("name: %d \n", json_object_get_int(value));

    isFound = json_object_object_get_ex(obj, "hobbies", &value);
    if (isFound) {
        const int len = json_object_array_length(value);
        printf("len: %d \n", len);

        for (int i = 0; i < len; ++i) {
            json_object *hobby = json_object_array_get_idx(value, i);
            if (hobby == NULL)
                continue;
            printf("hobby: %s \n", json_object_get_string(hobby));
        }
    }

    json_object_put(obj);
}
```

compile,

```sh
gcc main.c -g -ljson-c -o json-c-example
```

and validate by valgrind.

```sh
$ valgrind --leak-check=full ./json-c-example
ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```
