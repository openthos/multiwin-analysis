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

LocalePickerWithRegion是一个ListFragment，所以找到对应的adapter就可以了。上面的LocalePickerWithRegion对象是通过createLanguagePicker创建的，我们来看下这个函数。
```
 83     public static LocalePickerWithRegion createLanguagePicker(Context context,
 84             LocaleSelectedListener listener, boolean translatedOnly) {
 85         LocalePickerWithRegion localePicker = new LocalePickerWithRegion();
 86         localePicker.setListener(context, listener, /* parent */ null, translatedOnly);
 87         return localePicker;
 88     }
 ```
 直接new了一个对象，接着看onCreate方法实现
 ```
 139     @Override
140     public void onCreate(Bundle savedInstanceState) {
141         super.onCreate(savedInstanceState);
142         setHasOptionsMenu(true);
143 
144         if (mLocaleList == null) {
145             // The fragment was killed and restored by the FragmentManager.
146             // At this point we have no data, no listener. Just return, to prevend a NPE.
147             // Fixes b/28748150. Created b/29400003 for a cleaner solution.
148             returnToParentFrame();
149             return;
150         }
151 
152         final boolean countryMode = mParentLocale != null;
153         final Locale sortingLocale = countryMode ? mParentLocale.getLocale() : Locale.getDefault();
154         mAdapter = new SuggestedLocaleAdapter(mLocaleList, countryMode);
155         final LocaleHelper.LocaleInfoComparator comp =
156                 new LocaleHelper.LocaleInfoComparator(sortingLocale, countryMode);
157         mAdapter.sort(comp);
158         setListAdapter(mAdapter);
159     }
```
这里回去setListAdapter，给这个ListFragment设置adapter。我们已经找到了这个adapter，然后看它怎么被赋值的。mAdapter = new SuggestedLocaleAdapter(mLocaleList, countryMode); 就返回一个SuggestedLocaleAdapter，所以要看mLocaleList，语言列表都在这里面。去看mLocaleList是怎么被赋值的，直接搜索这个关键字，找到mLocaleList赋值的地方
```
103     private boolean setListener(Context context, LocaleSelectedListener listener,
104             LocaleStore.LocaleInfo parent, boolean translatedOnly) {
105         this.mParentLocale = parent;
106         this.mListener = listener;
107         this.mTranslatedOnly = translatedOnly;
108         setRetainInstance(true);
109 
110         final HashSet<String> langTagsToIgnore = new HashSet<>();
111         if (!translatedOnly) {
112             final LocaleList userLocales = LocalePicker.getLocales();
113             final String[] langTags = userLocales.toLanguageTags().split(",");
114             Collections.addAll(langTagsToIgnore, langTags);
115         }
116 
117         if (parent != null) {
118             mLocaleList = LocaleStore.getLevelLocales(context,
119                     langTagsToIgnore, parent, translatedOnly);
120             if (mLocaleList.size() <= 1) {
121                 if (listener != null && (mLocaleList.size() == 1)) {
122                     listener.onLocaleSelected(mLocaleList.iterator().next());
123                 }
124                 return false;
125             }
126         } else {
127             mLocaleList = LocaleStore.getLevelLocales(context, langTagsToIgnore,
128                     null /* no parent */, translatedOnly);
129         }
130 
131         return true;
132     }
```
赋值的地方是mLocaleList = LocaleStore.getLevelLocales(context, langTagsToIgnore, parent, translatedOnly)继续往下追，查看getLevelLocales实现
```
334     public static Set<LocaleInfo> getLevelLocales(Context context, Set<String> ignorables,
335             LocaleInfo parent, boolean translatedOnly) {
336         fillCache(context);
337         String parentId = parent == null ? null : parent.getId();
338 
339         HashSet<LocaleInfo> result = new HashSet<>();
340         for (LocaleStore.LocaleInfo li : sLocaleCache.values()) {
341             int level = getLevel(ignorables, li, translatedOnly);
342             if (level == 2) {
343                 if (parent != null) { // region selection
344                     if (parentId.equals(li.getParent().toLanguageTag())) {
345                         result.add(li);
346                     }
347                 } else { // language selection
348                     if (li.isSuggestionOfType(LocaleInfo.SUGGESTION_TYPE_SIM)) {
349                         result.add(li);
350                     } else {
351                         result.add(getLocaleInfo(li.getParent()));
352                     }
353                 }
354             }
355         }
356         return result;
357     }
```
这里就是循环把sLocaleCache中的值读取出来，所以要去找这个sLocaleCache在哪赋值的，赋值的地方就是fillCache这个函数。
```
249     public static void fillCache(Context context) {
250         if (sFullyInitialized) {
251             return;
252         }
253 
254         Set<String> simCountries = getSimCountries(context);
255 
256         for (String localeId : LocalePicker.getSupportedLocales(context)) {
257             if (localeId.isEmpty()) {
258                 throw new IllformedLocaleException("Bad locale entry in locale_config.xml");
259             }
260             LocaleInfo li = new LocaleInfo(localeId);
261             if (simCountries.contains(li.getLocale().getCountry())) {
262                 li.mSuggestionFlags |= LocaleInfo.SUGGESTION_TYPE_SIM;
263             }
264             sLocaleCache.put(li.getId(), li);
265             final Locale parent = li.getParent();
266             if (parent != null) {
267                 String parentId = parent.toLanguageTag();
268                 if (!sLocaleCache.containsKey(parentId)) {
269                     sLocaleCache.put(parentId, new LocaleInfo(parent));
270                 }
271             }
272         }
273 
274         boolean isInDeveloperMode = Settings.Global.getInt(context.getContentResolver(),
275                 Settings.Global.DEVELOPMENT_SETTINGS_ENABLED, 0) != 0;
276         for (String localeId : LocalePicker.getPseudoLocales()) {
277             LocaleInfo li = getLocaleInfo(Locale.forLanguageTag(localeId));
278             if (isInDeveloperMode) {
279                 li.setTranslated(true);
280                 li.mIsPseudo = true;
281                 li.mSuggestionFlags |= LocaleInfo.SUGGESTION_TYPE_SIM;
282             } else {
283                 sLocaleCache.remove(li.getId());
284             }
285         }
286 
287         // TODO: See if we can reuse what LocaleList.matchScore does
288         final HashSet<String> localizedLocales = new HashSet<>();
289         for (String localeId : LocalePicker.getSystemAssetLocales()) {
290             LocaleInfo li = new LocaleInfo(localeId);
291             final String country = li.getLocale().getCountry();
292             // All this is to figure out if we should suggest a country
293             if (!country.isEmpty()) {
294                 LocaleInfo cachedLocale = null;
295                 if (sLocaleCache.containsKey(li.getId())) { // the simple case, e.g. fr-CH
296                     cachedLocale = sLocaleCache.get(li.getId());
297                 } else { // e.g. zh-TW localized, zh-Hant-TW in cache
298                     final String langScriptCtry = li.getLangScriptKey() + "-" + country;
299                     if (sLocaleCache.containsKey(langScriptCtry)) {
300                         cachedLocale = sLocaleCache.get(langScriptCtry);
301                     }
302                 }
303                 if (cachedLocale != null) {
304                     cachedLocale.mSuggestionFlags |= LocaleInfo.SUGGESTION_TYPE_CFG;
305                 }
306             }
307             localizedLocales.add(li.getLangScriptKey());
308         }
309 
310         for (LocaleInfo li : sLocaleCache.values()) {
311             li.setTranslated(localizedLocales.contains(li.getLangScriptKey()));
312         }
313 
314         addSuggestedLocalesForRegion(Locale.getDefault());
315 
316         sFullyInitialized = true;
317     }
```
主要看LocalePicker.getSupportedLocales(context)，它就是去配置文档中把语言列表读取出来的，接着往下看getSupportedLocales实现
```
 92     public static String[] getSupportedLocales(Context context) {
 93         return context.getResources().getStringArray(R.array.supported_locales);
 94     }
 ```
 所以找到supported_locales这个数组就能找到加载的语言列表了。
 
8.supported_locales数组 

位置在frameworks/base/core/res/res/values/locale_config.xml

9.屏幕显示系统支持语言的名字

先找到语言列表的adapter，在LocalePickerWithRegion的oncreate方法中加载SuggestedLocaleAdapter，继续查看SuggestedLocaleAdapter中的getView方法，具体实现
```
162     @Override
163     public View getView(int position, View convertView, ViewGroup parent) {
164         if (convertView == null && mInflater == null) {
165             mInflater = LayoutInflater.from(parent.getContext());
166         }
167 
168         int itemType = getItemViewType(position);
169         switch (itemType) {
170             case TYPE_HEADER_SUGGESTED: // intentional fallthrough
171             case TYPE_HEADER_ALL_OTHERS:
172                 // Covers both null, and "reusing" a wrong kind of view
173                 if (!(convertView instanceof TextView)) {
174                     convertView = mInflater.inflate(R.layout.language_picker_section_header,
175                             parent, false);
176                 }
177                 TextView textView = (TextView) convertView;
178                 if (itemType == TYPE_HEADER_SUGGESTED) {
179                     setTextTo(textView, R.string.language_picker_section_suggested);
180                 } else {
181                     if (mCountryMode) {
182                         setTextTo(textView, R.string.region_picker_section_all);
183                     } else {
184                         setTextTo(textView, R.string.language_picker_section_all);
185                     }
186                 }
187                 textView.setTextLocale(
188                         mDisplayLocale != null ? mDisplayLocale : Locale.getDefault());
189                 break;
190             default:
191                 // Covers both null, and "reusing" a wrong kind of view
192                 if (!(convertView instanceof ViewGroup)) {
193                     convertView = mInflater.inflate(R.layout.language_picker_item, parent, false);
194                 }
195 
196                 TextView text = (TextView) convertView.findViewById(R.id.locale);
197                 LocaleStore.LocaleInfo item = (LocaleStore.LocaleInfo) getItem(position);
198                 text.setText(item.getLabel(mCountryMode));
199                 text.setTextLocale(item.getLocale());
200                 text.setContentDescription(item.getContentDescription(mCountryMode));
201                 if (mCountryMode) {
202                     int layoutDir = TextUtils.getLayoutDirectionFromLocale(item.getParent());
203                     //noinspection ResourceType
204                     convertView.setLayoutDirection(layoutDir);
205                     text.setTextDirection(layoutDir == View.LAYOUT_DIRECTION_RTL
206                             ? View.TEXT_DIRECTION_RTL
207                             : View.TEXT_DIRECTION_LTR);
208                 }
209         }
210         return convertView;
211     }
```
语言列表显示的name通过LocaleStore.LocaleInfo.getLabel(mCountryMode)加载显示，接着看getLabel方法
```
155         String getLabel(boolean countryMode) {
156             if (countryMode) {
157                 return getFullCountryNameNative();
158             } else {
159                 return getFullNameNative();
160             }
161         }
```
查看getFullCountryNameNative和getFullNameNative的实现
```
114         public String getFullNameNative() {
115             if (mFullNameNative == null) {
116                 mFullNameNative =
117                         LocaleHelper.getDisplayName(mLocale, mLocale, true /* sentence case */);
118             }
119             return mFullNameNative;
120         }
121 
122         String getFullCountryNameNative() {
123             if (mFullCountryNameNative == null) {
124                 mFullCountryNameNative = LocaleHelper.getDisplayCountry(mLocale, mLocale);
125             }
126             return mFullCountryNameNative;
127         }
```
查看LocaleHelper.getDisplayName和LocaleHelper.getDisplayCountry的实现
```
112     public static String getDisplayName(Locale locale, Locale displayLocale, boolean sentenceCase) {
113         final ULocale displayULocale = ULocale.forLocale(displayLocale);
114         String result = shouldUseDialectName(locale)
115                 ? ULocale.getDisplayNameWithDialect(locale.toLanguageTag(), displayULocale)
116                 : ULocale.getDisplayName(locale.toLanguageTag(), displayULocale);
117         return sentenceCase ? toSentenceCase(result, displayLocale) : result;
118     }

148     public static String getDisplayCountry(Locale locale) {
149         return ULocale.getDisplayCountry(locale.toLanguageTag(), ULocale.getDefault());
150     }
```
最终追到系统的字体库的保存位置external/icu/icu4c/source/data文件夹下，所有的系统的字体库都在这个目录下，系统通过LocalePicker.getSupportedLocales(context)获取字体库中对应语言的name显示在设置语言列表中。






















