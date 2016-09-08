```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
    <ImageView
        android:id="@+id/corner_lefttop"
        android:layout_alignParentLeft= "true"
        android:layout_alignParentTop="true"
        android:layout_width="@dimen/mw_shadow_padding"
        android:layout_height="@dimen/mw_shadow_padding"
        android:background="@drawable/shadow_lefttop"
        android:scaleType="fitXY" />
    <ImageView
        android:id="@+id/corner_righttop"
        android:layout_alignParentRight= "true"
        android:layout_alignParentTop="true"
        android:layout_width="@dimen/mw_shadow_padding"
        android:layout_height="@dimen/mw_shadow_padding"
        android:background="@drawable/shadow_righttop"
        android:scaleType="fitXY" />
    <ImageView
        android:id="@+id/corner_leftbottom"
        android:layout_alignParentLeft= "true"
        android:layout_alignParentBottom="true"
        android:layout_width="@dimen/mw_shadow_padding"
        android:layout_height="@dimen/mw_shadow_padding"
        android:background="@drawable/shadow_leftbottom"
        android:scaleType="fitXY" />
    <ImageView
        android:id="@+id/corner_rightbottom"
        android:layout_alignParentRight= "true"
        android:layout_alignParentBottom="true"
        android:layout_width="@dimen/mw_shadow_padding"
        android:layout_height="@dimen/mw_shadow_padding"
        android:background="@drawable/shadow_rightbottom"
        android:scaleType="fitXY" />
    <ImageView
        android:id="@+id/corner_left"
        android:layout_alignParentLeft= "true"
        android:layout_below="@id/corner_lefttop"
        android:layout_above="@id/corner_leftbottom"
        android:layout_width="@dimen/mw_shadow_padding"
        android:layout_height="1dp"
        android:background="@drawable/shadow_left"
        android:scaleType="fitXY" />
    <ImageView
        android:id="@+id/corner_top"
        android:layout_alignParentTop= "true"
        android:layout_toRightOf="@id/corner_lefttop"
        android:layout_toLeftOf="@id/corner_righttop"
        android:layout_width="1dp"
        android:layout_height="@dimen/mw_shadow_padding"
        android:background="@drawable/shadow_top"
        android:scaleType="fitXY" />
    <ImageView
        android:id="@+id/corner_right"
        android:layout_alignParentRight= "true"
        android:layout_below="@id/corner_righttop"
        android:layout_above="@id/corner_rightbottom"
        android:layout_width="@dimen/mw_shadow_padding"
        android:layout_height="1dp"
        android:background="@drawable/shadow_right"
        android:scaleType="fitXY" />
    <ImageView
        android:id="@+id/corner_bottom"
        android:layout_alignParentBottom= "true"
        android:layout_toRightOf="@id/corner_leftbottom"
        android:layout_toLeftOf="@id/corner_rightbottom"
        android:layout_width="1dp"
        android:layout_height="@dimen/mw_shadow_padding"
        android:background="@drawable/shadow_bottom"
        android:scaleType="fitXY" />
</RelativeLayout>
```
