```
adb shell "pm uninstall -k --user 0 com.miui.cleaner; pm uninstall -k --user 0 com.google.android.videos; pm uninstall -k --user 0 com.google.android.apps.nbu.paisa.user; pm uninstall -k --user 0 com.google.android.apps.googleassistant; pm uninstall -k --user 0 com.xiaomi.mipicks; pm uninstall -k --user 0 com.miui.player; pm uninstall -k --user 0 com.mi.android.globalFileexplorer; pm uninstall -k --user 0 com.android.mms"
```

```
for pkg in com.miui.cleaner \
com.google.android.videos \
com.google.android.apps.nbu.paisa.user \
com.google.android.apps.googleassistant \
com.xiaomi.mipicks \
com.miui.player \
com.mi.android.globalFileexplorer \
com.miui.gallery \
com.miui.android.fashiongallery \
com.google.android.apps.subscriptions.red; \
do adb shell pm uninstall -k --user 0 $pkg; done
```

```
com.android.mms \
```