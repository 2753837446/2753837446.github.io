---
layout:     post
title:      "Unity ScrollView 横竖嵌套完美解决方案"
subtitle:   "Unity & C#"
date:       2019-12-02 17:41:11
author:     "Luhao"
header-img: "img/post-bg-2015.jpg"
tags:
    - Unity
---

> “Here, We are!” 一起加油咯!

 ### 需求

> 需要实现横向滑动切换Page, 并且其中几个Page支持竖向滑动, 不能影响正常功能

#### 横向滑动代码

* 脚本配置在父ScrollView上, 配置页面宽度 和初始显示的页面index

```c#
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.EventSystems;
using UnityEngine.UI;

public class PageView : MonoBehaviour, IBeginDragHandler, IEndDragHandler {

    private ScrollRect rect; //滑动组件  
    private float targethorizontal = 0; //滑动的起始坐标  
    private bool isDrag = false; //是否拖拽结束  
    private List<float> posList = new List<float> (); //求出每页的临界角，页索引从0开始  
    private int currentPageIndex = -1;
    public Action<int> OnPageChanged;
    public RectTransform content;
    private bool stopMove = true;
    public float smooting = 4; //滑动速度  
    public float sensitivity = 0;
    private float startTime;

    private float startDragHorizontal;
    public Transform toggleList;
    public int ItemWidth;		//横向滑动页面宽度
    public int AutoIndex;
    void Start () {
        rect = transform.GetComponent<ScrollRect> ();
        var _rectWidth = GetComponent<RectTransform> ();
        var tempWidth = ((float) content.transform.childCount * _rectWidth.rect.width);
        // content.sizeDelta = new Vector2 (tempWidth, _rectWidth.rect.height);
        //未显示的长度
        float horizontalLength = content.rect.width - _rectWidth.rect.width;
        for (int i = 0; i < rect.content.transform.childCount; i++) {
            posList.Add (ItemWidth * i / horizontalLength);
        }
        pageTo (AutoIndex);
    }

    void Update () {
        if (!isDrag && !stopMove) {
            startTime += Time.deltaTime;
            float t = startTime * smooting;
            rect.horizontalNormalizedPosition = Mathf.Lerp (rect.horizontalNormalizedPosition, targethorizontal, t);
            if (t >= 1)
                stopMove = true;
            // Debug.Log(rect.horizontalNormalizedPosition);

        }
    }

    public void pageTo (int index) {
        if (index >= 0 && index < posList.Count) {
            rect.horizontalNormalizedPosition = posList[index];
            SetPageIndex (index);
            GetIndex (index);
        }
    }
    private void SetPageIndex (int index) {
        if (currentPageIndex != index) {
            currentPageIndex = index;
            if (OnPageChanged != null)
                OnPageChanged (index);
        }
    }

    public void OnBeginDrag (PointerEventData eventData) {
        isDrag = true;
        //开始拖动
        startDragHorizontal = rect.horizontalNormalizedPosition;
    }

    private void OnDisable () {
        pageTo (currentPageIndex);
    }

    public void OnEndDrag (PointerEventData eventData) {
        float posX = rect.horizontalNormalizedPosition;
        posX += ((posX - startDragHorizontal) * sensitivity);
        posX = posX < 1 ? posX : 1;
        posX = posX > 0 ? posX : 0;
        int index = 0;

        float offset = Mathf.Abs (posList[index] - posX);
        //Debug.Log("offset " + offset);

        for (int i = 1; i < posList.Count; i++) {
            float temp = Mathf.Abs (posList[i] - posX);
            //Debug.Log("temp " + temp);
            //Debug.Log("i" + i);
            if (temp < offset) {
                index = i;
                offset = temp;
            }
            //Debug.Log("index " + index);
        }
        //Debug.Log(index);
        SetPageIndex (index);
        GetIndex (index);
        targethorizontal = posList[index]; //设置当前坐标，更新函数进行插值  
        isDrag = false;
        startTime = 0;
        stopMove = false;


    }

    public void GetIndex (int index) {
        // var toogle = toggleList.GetChild(index).GetComponent<Toggle>();
        // toogle.isOn = true;
    }

}
```



#### 竖向滑动代码

* 只是正常滑动切换代码, 不包含竖向page切换, 如果需要竖向Page切换, 需要自己扩展
* 脚本配置在子ScrollView上, 即可实现横向竖向无缝切换

```c#
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.EventSystems;
using UnityEngine.UI;

public class ScrollRectEx : ScrollRect {

    private bool routeToParent = false;

    private void DoForParents<T> (Action<T> action) where T : IEventSystemHandler {
        Transform parent = transform.parent;
        while (parent != null) {
            foreach (var component in parent.GetComponents<Component> ()) {
                if (component is T) {
                    action ((T) (IEventSystemHandler) component);
                }
            }
            parent = parent.parent;
        }
    }

    /// <summary>
    /// Always route initialize potential drag event to parents
    /// </summary>
    public override void OnInitializePotentialDrag (PointerEventData eventData) {
        DoForParents<IInitializePotentialDragHandler> ((parent) => { parent.OnInitializePotentialDrag (eventData); });
        base.OnInitializePotentialDrag (eventData);
    }

    /// <summary>
    /// Drag event
    /// </summary>
    public override void OnDrag (UnityEngine.EventSystems.PointerEventData eventData) {
        if (routeToParent)
            DoForParents<IDragHandler> ((parent) => { parent.OnDrag (eventData); });
        else
            base.OnDrag (eventData);
    }

    /// <summary>
    /// Begin drag event
    /// </summary>
    public override void OnBeginDrag (UnityEngine.EventSystems.PointerEventData eventData) {
        if (!horizontal && Math.Abs (eventData.delta.x) > Math.Abs (eventData.delta.y))
            routeToParent = true;
        else if (!vertical && Math.Abs (eventData.delta.x) < Math.Abs (eventData.delta.y))
            routeToParent = true;
        else
            routeToParent = false;

        if (routeToParent)
            DoForParents<IBeginDragHandler> ((parent) => { parent.OnBeginDrag (eventData); });
        else
            base.OnBeginDrag (eventData);
    }

    /// <summary>
    /// End drag event
    /// </summary>
    public override void OnEndDrag (UnityEngine.EventSystems.PointerEventData eventData) {
        if (routeToParent)
            DoForParents<IEndDragHandler> ((parent) => { parent.OnEndDrag (eventData); });
        else
            base.OnEndDrag (eventData);
        routeToParent = false;
    }
}
```



