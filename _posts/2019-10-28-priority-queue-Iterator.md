---
title: PriorityQueue와 Iterator
categories: [TIL]
tags: [코딩테스트, 알고리즘]
excerpt: ""
---

### 출처

[https://programmers.co.kr/learn/courses/30/lessons/42626?language=java](https://programmers.co.kr/learn/courses/30/lessons/42626?language=java)

### 내 코드

    class Solution {
        public int solution(int[] scoville, int K) {
            int answer = 0;
            PriorityQueue<Integer> pq = new PriorityQueue<Integer>();
            for (int s: scoville)
                pq.add(s);
            
            int last = 0;
            Iterator<Integer> itr = pq.iterator();
            while(itr.hasNext()) {
                if (pq.peek() >= K)
                    return answer;
                if (pq.size() == 1) {
                    last = pq.poll();
                    break;
                }
                    
                int min = pq.poll();
                int min2 = pq.poll();
    
                pq.add(min + min2*2);
                answer++;
            }
            
            if (last >= K) {
                return answer;
            } else {
                return -1;
            }
        }
    }

# 배운 점

- PriortyQueue는 다음과 같은 method가 있다
    - add()
    - peek()
    - poll()
    - size()
    - remove()
    - toArray()
- pq.iterator()로 Iterator를 만들 수 있다.
    - Iterator.hasNext()로 loop를 편하게 돌릴 수 있음