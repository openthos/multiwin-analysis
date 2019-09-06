# 设置-系统-语言项显示加载流程

这里主要介绍如何去定制Settings中的语言，7.0以后Settings中的Languages & Input这项有了一些变化，不再是直接把语言列表显示出来了，而是选择了Add a language就会把所以的语言都列出来，再选择简体中文还会弹出一个列表，选择完一个语言后，在最开始的列表中就会多一个语言出来。

下面介绍语言的加载流程。
1.在AndroidMainfest.xml中找到Language Settings中
```
 625         <activity android:name=".Settings$LanguageAndInputSettingsActivity"
 626             android:label="@string/language_settings"
 627             android:icon="@drawable/ic_settings_language"
 628             android:taskAffinity="com.android.settings"
 629             android:parentActivityName="Settings$SystemDashboardActivity">
 630             <intent-filter>
 631                 <action android:name="android.intent.action.MAIN" />
 632                 <category android:name="android.intent.category.VOICE_LAUNCH" />
 633                 <category android:name="android.intent.category.DEFAULT" />
 634             </intent-filter>
 635             <intent-filter android:priority="260">
 636                 <action android:name="com.android.settings.action.SETTINGS"/>
 637             </intent-filter>
 638             <meta-data android:name="com.android.settings.category"
 639                        android:value="com.android.settings.category.ia.system"/>
 640             <meta-data android:name="com.android.settings.FRAGMENT_CLASS"
 641                        android:value="com.android.settings.language.LanguageAndInputSettings"/>
 642             <meta-data android:name="com.android.settings.PRIMARY_PROFILE_CONTROLLED"
 643                        android:value="true"/>
 644         </activity>
```
这里的LanguageAndInputSettingsActivity是个别名，真正的是
```
android:value="com.android.settings.language.LanguageAndInputSettings" />
```
2.LanguageAndInputSettings的分析

找到界面的布局文件
```
 87     @Override
 88     protected int getPreferenceScreenResId() {
 89         return R.xml.language_and_input;
 90     }
 ```
 3.language_and_input分析
 
 打开这个布局，第一个PreferenceScreen就是我们要找的语言设置界面。
 ```
  18 <PreferenceScreen
 19     xmlns:android="http://schemas.android.com/apk/res/android"
 20     xmlns:settings="http://schemas.android.com/apk/res/com.android.settings"
 21     android:title="@string/language_settings" >
 22 
 23     <Preference
 24         android:key="phone_language"
 25         android:title="@string/phone_language"
 26         android:icon="@drawable/ic_translate_24dp"
 27         android:fragment="com.android.settings.localepicker.LocaleListEditor"/>
 ```
4.LocaleListEditor分析 
 
这个类就是我们前面介绍的，有个Add a language按钮添加语言。我们要看这个按钮是怎么去加载语言的，所以要找到对应的按钮点击事件。这是个SettingsPreferenceFragment，我们先看onCreateView。
```
 75     @Override
 76     public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstState) {
 77         final View result = super.onCreateView(inflater, container, savedInstState);
 78         final View myLayout = inflater.inflate(R.layout.locale_order_list, (ViewGroup) result);
 79 
 80         getActivity().setTitle(R.string.pref_title_lang_selection);
 81 
 82         configureDragAndDrop(myLayout);
 83         return result;
 84     }
 ```
 这里有加载一个布局文件，上面是显示的语言，下面就是那个按钮。
 ```
  17 <ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
 18             android:layout_width="match_parent"
 19             android:layout_height="match_parent"
 20             android:layoutDirection="locale"
 21             android:textDirection="locale">
 22 
 23     <LinearLayout
 24         android:layout_width="match_parent"
 25         android:layout_height="match_parent"
 26         android:orientation="vertical"
 27         android:paddingTop="8dp"
 28         android:paddingBottom="8dp">
 29 
 30         <com.android.settings.localepicker.LocaleRecyclerView
 31             android:id="@+id/dragList"
 32             android:layout_width="match_parent"
 33             android:layout_height="wrap_content"
 34             android:scrollbars="vertical"/>
 35 
 36         <Button
 37             android:id="@+id/add_language"
 38             android:layout_width="match_parent"
 39             android:layout_height="?android:listPreferredItemHeight"
 40             android:paddingStart="?android:attr/listPreferredItemPaddingStart"
 41             android:paddingEnd="?android:attr/listPreferredItemPaddingEnd"
 42             android:drawableStart="@drawable/ic_add_24dp"
 43             android:drawablePadding="24dp"
 44             android:textAlignment="textStart"
 45             android:text="@string/add_a_language"
 46             style="@style/Base.Widget.AppCompat.Button.Borderless"
 47             android:textAppearance="?android:attr/textAppearanceListItem"/>
 48 
 49     </LinearLayout>
 50 
 51 </ScrollView>
```
然后就去LocaleListEditor中寻找add_language这个按钮的点击事件。看它是如何加载语言列表并显示的。
```
234     private void configureDragAndDrop(View view) {
235         final RecyclerView list = (RecyclerView) view.findViewById(R.id.dragList);
236         final LocaleLinearLayoutManager llm = new LocaleLinearLayoutManager(getContext(), mAdapter);
237         llm.setAutoMeasureEnabled(true);
238         list.setLayoutManager(llm);
239 
240         list.setHasFixedSize(true);
241         mAdapter.setRecyclerView(list);
242         list.setAdapter(mAdapter);
243 
244         mAddLanguage = view.findViewById(R.id.add_language);
245         mAddLanguage.setOnClickListener(new View.OnClickListener() {
246             @Override
247             public void onClick(View v) {
248                 final LocalePickerWithRegion selector = LocalePickerWithRegion.createLanguagePicker(
249                         getContext(), LocaleListEditor.this, false /* translate only */);
250                 getFragmentManager()
251                         .beginTransaction()
252                         .setTransition(FragmentTransaction.TRANSIT_FRAGMENT_OPEN)
253                         .replace(getId(), selector)
254                         .addToBackStack("localeListEditor")
255                         .commit();
256             }
257         });
258     }
```
这边是新建了一个LocalePickerWithRegion对象，然后把它作为新的fragment来显示，所以就是这里去加载并显示语言列表的。

5.LocalePickerWithRegion分析 























