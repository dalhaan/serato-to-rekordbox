# serato-to-rekordbox

Crate format:
Consists of tags.

Tag format:
Tags consist of a tag type (four byte ASCII string), payload length in bytes (unsigned 32-bit big endian integer), and payload (of payload length in size).

Tags identified:

1. 'vrsn' - crate metadata
  - payload length: 56 bytes
  - payload contains:
    - ASCII text

example:

ASCII || v  r  s  n  |          8  |    1     .     0     /     S     e     r     a     t     o           S     c     r     a     t     c     h     L     i     v     e           C     r     a     t     e
------||-------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------
   0x || 76 72 73 6e | 00 00 00 38 | 00 31 00 2e 00 30 00 2f 00 53 00 65 00 72 00 61 00 74 00 6f 00 20 00 53 00 63 00 72 00 61 00 74 00 63 00 68 00 4c 00 69 00 76 00 65 00 20 00 43 00 72 00 61 00 74 00 65
------||-------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------
      ||  tag type   |    payload  |   payload
      ||             |    length   |


2. 'ostr' - ?first tag?
  - payload length: 25 bytes
  - payload contains:
    - 1x 'tvcn' tag
    - 1x 'brev' tag

3. 'ovct' - Track details column 

3. 'tvcn' - track details column name
  - payload length: varies depending on the column name
  - payload contains:
    - column name (characters separated by null bytes like all text in this file)

4. 'brev' - ??? only seems to appear in 'ostr' tag payload
  - payload length: 1
  - payload contains:
    - ??? is only one byte so maybe some boolean

5. 'ovct' - 


'./files/crates/D - Liquid.crate':
i | 0x | ASCII
--|----|-------
0 | 76 | v  <-|
1 | 72 | r    |- First four bytes is the tag ID for the metadata
2 | 73 | s    |
3 | 6e | n  <-|
4 | 00 |    <-|
5 | 00 |      |- next 4 bytes is the length (Unsigned BE 32-bit integer) of the tag
6 | 00 |      |  (our case 0x38 = 56)
7 | 38 | 8  <-|
8 | 00 |    <-|
9 | 31 | 1    |
10 | 00 |     |
11 | 2e | .
12 | 00 | 
13 | 30 | 0
14 | 00 | 
15 | 2f | /
16 | 00 | 
17 | 53 | S
18 | 00 | 
19 | 65 | e
20 | 00 | 
21 | 72 | r
22 | 00 | 
23 | 61 | a
24 | 00 | 
25 | 74 | t
26 | 00 | 
27 | 6f | o
28 | 00 | 
29 | 20 |  
30 | 00 | 
31 | 53 | S
32 | 00 | 
33 | 63 | c
34 | 00 | 
35 | 72 | r
36 | 00 | 
37 | 61 | a
38 | 00 | 
39 | 74 | t
40 | 00 | 
41 | 63 | c
42 | 00 | 
43 | 68 | h
44 | 00 | 
45 | 4c | L
46 | 00 | 
47 | 69 | i
48 | 00 | 
49 | 76 | v
50 | 00 | 
51 | 65 | e
52 | 00 | 
53 | 20 |  
54 | 00 | 
55 | 43 | C
56 | 00 | 
57 | 72 | r
58 | 00 | 
59 | 61 | a
60 | 00 | 
61 | 74 | t   |
62 | 00 |     |
63 | 65 | e <-|
64 | 6f | o <-|
65 | 73 | s   |- next 4 bytes is the tag type
66 | 72 | r   |
67 | 74 | t <-|
68 | 00 |   <-|
69 | 00 |     |- next 4 bytes is length of tag (32-bit integer)
70 | 00 |     |  (our case 0x19 = 25 bytes)
71 | 19 |   <-|- next 'tag length' bytes is the payload, which seems to be nested tags for the 'osrt' tag
72 | 74 | t   | <-|
73 | 76 | v   |   |- first 4 bytes is tag type ('tvcn')
74 | 63 | c   |   |
75 | 6e | n   | <-|
76 | 00 |     | <-|
77 | 00 |     |   |- next 4 bytes is tag length (32-bit integer)
78 | 00 |     |   |  (our case 0x08 = 8 bytes)
79 | 08 |     | <-|
80 | 00 |     | <-|
81 | 73 | s   |   |
82 | 00 |     |   |
83 | 6f | o   |   |- next 'payload length' bytes is column name for 'tvcn' tag
84 | 00 |     |   |  (our case 'song' with the null bytes removed)
85 | 6e | n   |   |
86 | 00 |     |   |
87 | 67 | g   | <-|
88 | 62 | b   | <-|
89 | 72 | r   |   |- next 4 bytes is tag type ('brev')
90 | 65 | e   |   |
91 | 76 | v   | <-|
92 | 00 |     | <-|
93 | 00 |     |   |- next 4 bytes is payload length (32-bit integer)
94 | 00 |     |   |  (our case 0x01 = 1 byte)
95 | 01 |     | <-|
96 | 00 |   <-| <--- single byte payload (probably boolean)
97 | 6f | o   <-|
98 | 76 | v     |- tag type ('ovct')
99 | 63 | c     |
100 | 74 | t  <-|
101 | 00 |    <-|
102 | 00 |      |- payload length (0x24 = 36 bytes)
103 | 00 |      |
104 | 24 | $  <-|
105 | 74 | t  <-| <-|
106 | 76 | v    |   |- tag type ('tvcn' - column)
107 | 63 | c    |   |
108 | 6e | n    | <-|
109 | 00 |      | <-|
110 | 00 |      |   |- payload length (0x12 = 18 bytes)
111 | 00 |      |   |
112 | 12 |      | <-|
113 | 00 |      |   |
114 | 70 | p    |   |
115 | 00 |      |   |
116 | 6c | l    |   |
117 | 00 |      |   |
118 | 61 | a    |   |
119 | 00 |      |   |
120 | 79 | y    |   |- payload ('playCount')
121 | 00 |      |   |
122 | 43 | C    |   |
123 | 00 |      |   |
124 | 6f | o    |   |
125 | 00 |      |   |
126 | 75 | u    |   |
127 | 00 |      |   |
128 | 6e | n    |   |
129 | 00 |      |   |
130 | 74 | t    | <-|
131 | 74 | t    | <-|
132 | 76 | v    |   |- tag type ('tvcw')
133 | 63 | c    |   |
134 | 77 | w    | <-|
135 | 00 |      | <-|
136 | 00 |      |   |- payload length (0x02 = 2 bytes)
137 | 00 |      |   |
138 | 02 |      | <-|
139 | 00 |      | <-|- payload (I think may be ASCII: '0' (minus nullbytes))
140 | 30 | 0  <-| <-|
141 | 6f | o
142 | 76 | v
143 | 63 | c
144 | 74 | t
145 | 00 | 
146 | 00 | 
147 | 00 | 
148 | 18 | 
149 | 74 | t
150 | 76 | v
151 | 63 | c
152 | 6e | n
153 | 00 | 
154 | 00 | 
155 | 00 | 
156 | 06 | 
157 | 00 | 
158 | 62 | b
159 | 00 | 
160 | 70 | p
161 | 00 | 
162 | 6d | m
163 | 74 | t
164 | 76 | v
165 | 63 | c
166 | 77 | w
167 | 00 | 
168 | 00 | 
169 | 00 | 
170 | 02 | 
171 | 00 | 
172 | 30 | 0
173 | 6f | o
174 | 76 | v
175 | 63 | c
176 | 74 | t
177 | 00 | 
178 | 00 | 
179 | 00 | 
180 | 1e | 
181 | 74 | t
182 | 76 | v
183 | 63 | c
184 | 6e | n
185 | 00 | 
186 | 00 | 
187 | 00 | 
188 | 0c | 
189 | 00 | 
190 | 6c | l
191 | 00 | 
192 | 65 | e
193 | 00 | 
194 | 6e | n
195 | 00 | 
196 | 67 | g
197 | 00 | 
198 | 74 | t
199 | 00 | 
200 | 68 | h
201 | 74 | t
202 | 76 | v
203 | 63 | c
204 | 77 | w
205 | 00 | 
206 | 00 | 
207 | 00 | 
208 | 02 | 
209 | 00 | 
210 | 30 | 0
211 | 6f | o
212 | 76 | v
213 | 63 | c
214 | 74 | t
215 | 00 | 
216 | 00 | 
217 | 00 | 
218 | 22 | "
219 | 74 | t
220 | 76 | v
221 | 63 | c
222 | 6e | n
223 | 00 | 
224 | 00 | 
225 | 00 | 
226 | 0e | 
227 | 00 | 
228 | 63 | c
229 | 00 | 
230 | 6f | o
231 | 00 | 
232 | 6d | m
233 | 00 | 
234 | 6d | m
235 | 00 | 
236 | 65 | e
237 | 00 | 
238 | 6e | n
239 | 00 | 
240 | 74 | t
241 | 74 | t
242 | 76 | v
243 | 63 | c
244 | 77 | w
245 | 00 | 
246 | 00 | 
247 | 00 | 
248 | 04 | 
249 | 00 | 
250 | 37 | 7
251 | 00 | 
252 | 35 | 5
253 | 6f | o
254 | 76 | v
255 | 63 | c
256 | 74 | t
257 | 00 | 
258 | 00 | 
259 | 00 | 
260 | 1c | 
261 | 74 | t
262 | 76 | v
263 | 63 | c
264 | 6e | n
265 | 00 | 
266 | 00 | 
267 | 00 | 
268 | 06 | 
269 | 00 | 
270 | 6b | k
271 | 00 | 
272 | 65 | e
273 | 00 | 
274 | 79 | y
275 | 74 | t
276 | 76 | v
277 | 63 | c
278 | 77 | w
279 | 00 | 
280 | 00 | 
281 | 00 | 
282 | 06 | 
283 | 00 | 
284 | 32 | 2
285 | 00 | 
286 | 30 | 0
287 | 00 | 
288 | 30 | 0
289 | 6f | o
290 | 76 | v
291 | 63 | c
292 | 74 | t
293 | 00 | 
294 | 00 | 
295 | 00 | 
296 | 22 | "
297 | 74 | t
298 | 76 | v
299 | 63 | c
300 | 6e | n
301 | 00 | 
302 | 00 | 
303 | 00 | 
304 | 0e | 
305 | 00 | 
306 | 62 | b
307 | 00 | 
308 | 69 | i
309 | 00 | 
310 | 74 | t
311 | 00 | 
312 | 72 | r
313 | 00 | 
314 | 61 | a
315 | 00 | 
316 | 74 | t
317 | 00 | 
318 | 65 | e
319 | 74 | t
320 | 76 | v
321 | 63 | c
322 | 77 | w
323 | 00 | 
324 | 00 | 
325 | 00 | 
326 | 04 | 
327 | 00 | 
328 | 38 | 8
329 | 00 | 
330 | 32 | 2
331 | 6f | o
332 | 76 | v
333 | 63 | c
334 | 74 | t
335 | 00 | 
336 | 00 | 
337 | 00 | 
338 | 1a | 
339 | 74 | t
340 | 76 | v
341 | 63 | c
342 | 6e | n
343 | 00 | 
344 | 00 | 
345 | 00 | 
346 | 08 | 
347 | 00 | 
348 | 73 | s
349 | 00 | 
350 | 6f | o
351 | 00 | 
352 | 6e | n
353 | 00 | 
354 | 67 | g
355 | 74 | t
356 | 76 | v
357 | 63 | c
358 | 77 | w
359 | 00 | 
360 | 00 | 
361 | 00 | 
362 | 02 | 
363 | 00 | 
364 | 30 | 0
365 | 6f | o
366 | 76 | v
367 | 63 | c
368 | 74 | t
369 | 00 | 
370 | 00 | 
371 | 00 | 
372 | 22 | "
373 | 74 | t
374 | 76 | v
375 | 63 | c
376 | 6e | n
377 | 00 | 
378 | 00 | 
379 | 00 | 
380 | 0c | 
381 | 00 | 
382 | 61 | a
383 | 00 | 
384 | 72 | r
385 | 00 | 
386 | 74 | t
387 | 00 | 
388 | 69 | i
389 | 00 | 
390 | 73 | s
391 | 00 | 
392 | 74 | t
393 | 74 | t
394 | 76 | v
395 | 63 | c
396 | 77 | w
397 | 00 | 
398 | 00 | 
399 | 00 | 
400 | 06 | 
401 | 00 | 
402 | 32 | 2
403 | 00 | 
404 | 35 | 5
405 | 00 | 
406 | 38 | 8
407 | 6f | o
408 | 76 | v
409 | 63 | c
410 | 74 | t
411 | 00 | 
412 | 00 | 
413 | 00 | 
414 | 1c | 
415 | 74 | t
416 | 76 | v
417 | 63 | c
418 | 6e | n
419 | 00 | 
420 | 00 | 
421 | 00 | 
422 | 0a | 
423 | 00 | 
424 | 61 | a
425 | 00 | 
426 | 6c | l
427 | 00 | 
428 | 62 | b
429 | 00 | 
430 | 75 | u
431 | 00 | 
432 | 6d | m
433 | 74 | t
434 | 76 | v
435 | 63 | c
436 | 77 | w
437 | 00 | 
438 | 00 | 
439 | 00 | 
440 | 02 | 
441 | 00 | 
442 | 30 | 0
443 | 6f | o
444 | 76 | v
445 | 63 | c
446 | 74 | t
447 | 00 | 
448 | 00 | 
449 | 00 | 
450 | 1c | 
451 | 74 | t
452 | 76 | v
453 | 63 | c
454 | 6e | n
455 | 00 | 
456 | 00 | 
457 | 00 | 
458 | 0a | 
459 | 00 | 
460 | 61 | a
461 | 00 | 
462 | 64 | d
463 | 00 | 
464 | 64 | d
465 | 00 | 
466 | 65 | e
467 | 00 | 
468 | 64 | d
469 | 74 | t
470 | 76 | v
471 | 63 | c
472 | 77 | w
473 | 00 | 
474 | 00 | 
475 | 00 | 
476 | 02 | 
477 | 00 | 
478 | 30 | 0
479 | 6f | o
480 | 76 | v
481 | 63 | c
482 | 74 | t
483 | 00 | 
484 | 00 | 
485 | 00 | 
486 | 22 | "
487 | 74 | t
488 | 76 | v
489 | 63 | c
490 | 6e | n
491 | 00 | 
492 | 00 | 
493 | 00 | 
494 | 10 | 
495 | 00 | 
496 | 66 | f
497 | 00 | 
498 | 69 | i
499 | 00 | 
500 | 6c | l
501 | 00 | 
502 | 65 | e
503 | 00 | 
504 | 6e | n
505 | 00 | 
506 | 61 | a
507 | 00 | 
508 | 6d | m
509 | 00 | 
510 | 65 | e
511 | 74 | t
512 | 76 | v
513 | 63 | c
514 | 77 | w
515 | 00 | 
516 | 00 | 
517 | 00 | 
518 | 02 | 
519 | 00 | 
520 | 30 | 0
521 | 6f | o
522 | 76 | v
523 | 63 | c
524 | 74 | t
525 | 00 | 
526 | 00 | 
527 | 00 | 
528 | 22 | "
529 | 74 | t
530 | 76 | v
531 | 63 | c
532 | 6e | n
533 | 00 | 
534 | 00 | 
535 | 00 | 
536 | 10 | 
537 | 00 | 
538 | 6c | l
539 | 00 | 
540 | 6f | o
541 | 00 | 
542 | 63 | c
543 | 00 | 
544 | 61 | a
545 | 00 | 
546 | 74 | t
547 | 00 | 
548 | 69 | i
549 | 00 | 
550 | 6f | o
551 | 00 | 
552 | 6e | n
553 | 74 | t
554 | 76 | v
555 | 63 | c
556 | 77 | w
557 | 00 | 
558 | 00 | 
559 | 00 | 
560 | 02 | 
561 | 00 | 
562 | 30 | 0
563 | 6f | o
564 | 74 | t
565 | 72 | r
566 | 6b | k
567 | 00 | 
568 | 00 | 
569 | 00 | 
570 | 68 | h
571 | 70 | p
572 | 74 | t
573 | 72 | r
574 | 6b | k
575 | 00 | 
576 | 00 | 
577 | 00 | 
578 | 60 | `
579 | 00 | 
580 | 55 | U
581 | 00 | 
582 | 73 | s
583 | 00 | 
584 | 65 | e
585 | 00 | 
586 | 72 | r
587 | 00 | 
588 | 73 | s
589 | 00 | 
590 | 2f | /
591 | 00 | 
592 | 67 | g
593 | 00 | 
594 | 75 | u
595 | 00 | 
596 | 65 | e
597 | 00 | 
598 | 73 | s
599 | 00 | 
600 | 74 | t
601 | 00 | 
602 | 32 | 2
603 | 00 | 
604 | 2f | /
605 | 00 | 
606 | 4d | M
607 | 00 | 
608 | 75 | u
609 | 00 | 
610 | 73 | s
611 | 00 | 
612 | 69 | i
613 | 00 | 
614 | 63 | c
615 | 00 | 
616 | 2f | /
617 | 00 | 
618 | 44 | D
619 | 00 | 
620 | 6e | n
621 | 00 | 
622 | 42 | B
623 | 00 | 
624 | 2f | /
625 | 00 | 
626 | 4f | O
627 | 00 | 
628 | 77 | w
629 | 00 | 
630 | 6e | n
631 | 00 | 
632 | 67 | g
633 | 00 | 
634 | 6c | l
635 | 00 | 
636 | 6f | o
637 | 00 | 
638 | 77 | w
639 | 00 | 
640 | 20 |  
641 | 00 | 
642 | 2d | -
643 | 00 | 
644 | 20 |  
645 | 00 | 
646 | 41 | A
647 | 00 | 
648 | 6e | n
649 | 00 | 
650 | 67 | g
651 | 00 | 
652 | 65 | e
653 | 00 | 
654 | 6c | l
655 | 00 | 
656 | 73 | s
657 | 00 | 
658 | 20 |  
659 | 00 | 
660 | 53 | S
661 | 00 | 
662 | 69 | i
663 | 00 | 
664 | 6e | n
665 | 00 | 
666 | 67 | g
667 | 00 | 
668 | 2e | .
669 | 00 | 
670 | 6d | m
671 | 00 | 
672 | 70 | p
673 | 00 | 
674 | 33 | 3
