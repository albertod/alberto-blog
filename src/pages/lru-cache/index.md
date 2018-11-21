---
title: LRU Cache
date: '2018-11-18T15:13:47Z'
---

While practicing (and honestly remembering) some fundamentals computer science concepts, I came across the problem of designing an LRU cache. For those who don’t know what and LRU cache is, it is incomplete terms a Least Recently Used cache. What this means is that the cache will use a strategy if its space of entries its full and it needs to add a new entry it will replace the oldest used entry (oldest meaning the entry that has been in the cache for more time without being read or push into the cache for the longest time).

We shouldn’t have a hard time n the simple scenario we have enough space to hold all the possible options in our problem. Simply you can think of a Map data structure. But what happens when our problem scenario have a space of option very big, even unlimited different options? Well here if we are talking about a single threaded problem setup an LRU cache will be our best option.

So how do we implement an LRU cache in Java

<!-- LRU cache in JAVA using Linked List and a Map -->

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

<!-- HashedLinkList  -->

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

I'm sure I'll write a lot more interesting things in the future.
