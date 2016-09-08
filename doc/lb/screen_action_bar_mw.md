
```
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@drawable/mw_shadow_outside"
    android:padding="@dimen/mw_shadow_outside_border">
    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@drawable/mw_shadow_middle"
        android:padding="@dimen/mw_shadow_middle_border">
        <FrameLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="@drawable/mw_shadow_inside"
            android:padding="@dimen/mw_shadow_inside_border">
            <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:id="@+id/mwOuterBorder"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:background="@drawable/mw_outer_border"
                android:padding="@dimen/mw_outer_border"
                android:orientation="vertical">
                <include layout="@layout/mw_decor"/>
                <include layout="@layout/screen_action_bar"/>
            </LinearLayout>
            <include layout="@layout/mw_cover"/>
        </FrameLayout>
    </FrameLayout>
    <include layout="@layout/mw_shadow"/>
</FrameLayout>
```
