---
title: 生成树算法检查管网拓扑关系
date: 2016-10-1 16:12:57
categories: blog
tags: [algorithm,tree]
---

检查从autoCAD导出的管线链接的合法性，判断每个节点的分支情况，防止有悬空的情况发生。

核心代码从每个线段生成网络

``` bash
public void parserNet()
        {
            for (int i = 0; i < dicColorIndex.Count;i++ )
            {
                dicIndexColor.Add(i, dicColorIndex.Keys.ElementAt(i));
                List<Vertex> branches = vtxLists[i];
                //log.WriteLine(string.Format("brances[{0}] count={1}",i, branches.Count));
                Queue < Vertex >  queue = new Queue<Vertex>(branches.Count);
                foreach (Vertex s in branches)
                {
                    //printSub(s);
                    queue.Enqueue(s);
                }
                while (queue.Count > 0)
                {
                    Vertex sub = queue.Dequeue();
             
                    Vertex p = findInList(branches,sub.id, sub.pos.x, sub.pos.y);
                    if (p == null || p.id == sub.id)
                    {
                        subTrees[i].Add(sub);                        
                        Console.WriteLine(string.Format("sub index[{0}] edge[{1}] not find Parent ",sub.id,sub.id/2));
                        log.WriteLine(string.Format("*erro: 第[{0:000}]行-[53{1},464{2}]->未接入网", sub.id / 2, sub.pos.x, sub.pos.y));
                        errMsg.Add(string.Format("*erro: 第[{0:000}]行-[53{1},464{2}]->未接入网", sub.id / 2, sub.pos.x, sub.pos.y));
                    }
                    else
                    {
                        sub.v0 = p;
                        if (p.i == 1)
                        {
                            p.v1 = sub;
                            //Console.WriteLine(string.Format("P index[{0}] find next v[{1}]"),p.id,sub.id);
                        }
                        else if (p.i == 2)
                        {
                            p.v2 = sub;
                            //Console.WriteLine(string.Format("P index[{0}] find branch v[{1}]"), p.id, sub.id);
                        }
                    }                   
                }
            }
```

view code: [github](https://hexo.io/docs/deployment.html)
