آموزش کامل نصب و راه اندازی نرم افزار passwall2 بر روی سیستم عامل OpenWRT مخصوص نسخه ۲۵.۱۲.۴ :


تغییرات:
-۲۵.۱۲.۴
-تغییر پیشفرض تنظیمات پسوال از فایل متنی به دستورات uci. 
-تغییر از opkg به apk 
-حذف بسته wget-ssl از پیشنیازها.
-۲۴.۱۰.۶
-امکان نصب sing-box تنها یا xray-core تنها یا هر دو.
-فوروارد کردن تمام ترافیک tcp بجای پورتهای متداول.
-اضافه شدن لیست وبسایتهای ایرانی دارای ضعف امنیتی rebind attack.
-اضافه شدن اسکریپت مخصوص اضافه کردن لیست آدرسهای ایران.
-اضافه شدن بسته wget-ssl برای رفع ایراد بروزرسانی رپوها در sourceforge ( با تشکر از اعضای گروه برای ارائه راهکار )

دقت کنید این آموزش مخصوص نسخه پایدار ۲۵.۱۲.۴ میباشد.

دانلود و نصب آخرین نسخه OpenWRT از وبسایت اصلی  :
https://downloads.openwrt.org/releases/25.12.4/targets/

بروز رسانی مخازن:
apk update
حذف بسته dnsmasq ساده و نصب dnsmasq-full :
apk del dnsmasq
apk add dnsmasq-full
نصب بسته های پیش نیاز:
apk add kmod-nft-tproxy kmod-nft-socket
گرفتن کلیدهای مخزن پسوال۲ : 
wget -O /etc/apk/keys/passwall.pem https://master.dl.sourceforge.net/project/openwrt-passwall-build/apk.pub -O /etc/apk/keys/passwall.pem
اضافه کردن مخزن پسوال۲ : 
read release arch << EOF
$(. /etc/openwrt_release ; echo ${DISTRIB_RELEASE%.*} $DISTRIB_ARCH)
EOF
for feed in passwall_packages passwall2; do
  echo "https://master.dl.sourceforge.net/project/openwrt-passwall-build/releases/packages-$release/$arch/$feed/packages.adb" >> /etc/apk/repositories.d/customfeeds.list
done
بروز رسانی مخازن و نصب بسته های اصلی :
apk update
apk add luci-app-passwall2


در صورت نیاز به فقط استفاده از هسته سینگ باکس :
apk add sing-box

در صورت استفاده از هسته xray  :
apk add xray-core v2ray-geosite-ir

حذف قوانین فعلی :
for s in $(uci show passwall2 | sed -n 's/^passwall2\.\([^=]*\)=shunt_rules$/\1/p'); do uci delete passwall2.$s; done && uci commit passwall2

اضافه کردن لیست دامنه ها و آدرس های ایرانی (بر پایه bootmortis) :
uci -q delete passwall2.Iran

uci set passwall2.Iran='shunt_rules'
uci set passwall2.Iran.remarks='Iran'
uci set passwall2.Iran.network='tcp,udp'
uci set passwall2.Iran.domain_list='geosite:category-ir
ext:iran.dat:all'
uci set passwall2.Iran.ip_list='geoip:ir'

uci commit passwall2


-اضافه کردن لیست وبسایتهای ایرانی دارای ضعف امنیتی rebind attack. (اختیاری):
uci add_list dhcp.@dnsmasq[0].rebind_domain='banksepah.ir'
uci add_list dhcp.@dnsmasq[0].rebind_domain='ebanksepah.ir'
uci add_list dhcp.@dnsmasq[0].rebind_domain='gov.ir'
uci add_list dhcp.@dnsmasq[0].rebind_domain='medu.ir'
uci add_list dhcp.@dnsmasq[0].rebind_domain='qmb.ir'
uci add_list dhcp.@dnsmasq[0].rebind_domain='tamin.ir'
uci commit dhcp

-لینک گیتهاب مربوط به رپوی پسوال۲ جهت نصب کلید و رپوهای مناسب.
https://github.com/MoetaYuko/openwrt-passwall-build
#openwrt #passwall2
