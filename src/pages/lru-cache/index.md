---
title: LRU Cache
date: '2018-11-18T15:13:47Z'
---

While practicing (and honestly remembering) some fundamentals computer science concepts, I came across the problem of designing an LRU cache. For those who don’t know what and LRU cache is, it is in complete terms a Least Recently Used cache. What this means is that the cache will use the following strategy: if its space of entries its full and it needs to add a new entry it will replace the oldest used entry (oldest meaning the entry that has been in the cache for more time without being read or pushed into the cache for the longest time).

We shouldn’t have a hard time with the simple scenario where we have enough space to hold all the possible options in our problem. Simply you can think of a Map data structure. But what happens when our problem scenario have a space of options very big, even unlimited different options? Well, here if we are talking about a single threaded problem setup an LRU cache will be our best option.

So how do we implement an LRU cache in Java

```java{numberLines: true}
public class Solution {
    private int c;
    private HashMap<Integer,Integer> hm= new HashMap<Integer,Integer>();
    private ArrayList<Integer> l= new ArrayList<Integer>();
    private int count=0;
    
    public Solution(int capacity) {
        this.c= capacity;
    }
    
    /*
    list with keys
    list with key and value
    */
    
    public int get(int key) {
            if(hm.containsKey(key)){
                removeElement(key);
                addInFront(key,hm.get(key));
                return hm.get(key);
            }
            else{
                return -1;
            }
    }
    
    public void set(int key, int value) {
        if(!hm.containsKey(key)){
            if(count < c){
                addInFront(key,value);
            }
            else{
                removeLast();
                addInFront(key,value);
                
            }
        }
        else{
            removeElement(key);
            addInFront(key,value);
        }
        
    }
    
    private void removeElement(int no){
        int index= l.indexOf(no);
        l.remove(index);
        count--;
    }
    
    private void addInFront(int key,int no){
        l.add(0,key);
        hm.put(key,no);
        count++;
    }
    
    private void removeLast(){
        int key=l.get(l.size()-1);
        l.remove(l.size()-1);
        hm.remove(key);
        count--;
    }
}
```

In the previous code block, we are creating an LRU cache for a single thread node. The application consists of creating an ArrayList that will act as an access queue. Basically, it will keep track of the usage time order for each key, by holding a reference to the data Node and keep the order of access. Then simply when our ArrayList is full and a query (put/get) request is received we know the last element of the Array is the least recently used so we can remove it and place the new element on the first position of the ArrayList.

This was an interesting exercise for me because it allowed me to review some concepts like memory reference, linked lists, and array ordering.

After completing the exercise I was sure that somebody already removed the complexity of this task by creating a utility class, and I was correct. Checking the Java docs for the Map interface I found the following class: LinkedHashMap. 

Reading the [docs](https://docs.oracle.com/javase/8/docs/api/java/util/LinkedHashMap.html) I found the following: 

>A special constructor is provided to create a linked hash map whose order of iteration is the order in which its entries were last accessed, from least-recently accessed to most-recently (access-order). This kind of map is well-suited to building LRU caches.

Then all the complexity of the previous code block became:

<!-- HashedLinkList  -->
```java{numberLines: true}
public class Solution {

        private MyCache<Integer, Integer> cache;

        Solution(int capacity) {
            this.cache = new MyCache<>(capacity);
        }

        public int get(int key) {
            return this.cache.getOrDefault(key, -1);
        }

        public void set(int key, int value) {
            this.cache.put(key, value);
        }

        private class MyCache<T, E> extends LinkedHashMap<T, E> {
            private Integer capacity;
            MyCache(Integer capacity) {
                super(capacity, 0.75f, true);
                this.capacity = capacity;
            }

            @Override
            protected boolean removeEldestEntry(Map.Entry<T, E> eldest) {
                return this.size() > this.capacity;
            }
        }
}
```

Leason learned ... check the Javadocs and you might find a surprise ;)