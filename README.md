# speach-2-test--test-rec-2-hardwere
المهمة الاولى 
تحويل الصوت الى النص
go to https://s-m.com.sa/r2/test/ 
and speach to convert the it to test 
go to Google Cloud speacg API 
https://cloud.google.com/speech-to-text

المهمة الثانيه 
تفاعل النص مع الهاردوير 
كتابة خوارزمية تشغيل قطعة wasdom ESP32

الأدوات البرمجية Toolchain
سلسلة الأدوات toolchain.
الـ ESP-IDF من مستودع Expressif.

إن الخرج التالي يجب أن يظهر بعد تنزيل وتهيئة ناجحة لما سبق عبر إرسال الأوامر التالية عبر التيرمينال في لينكس أو MinGW في الويندوز الموجود داخل ESP-IDF:

Command: echo $IDF_PATH
Output: \your\path\to\esp-idf
Command: printenv PATH
Output: /home/user-name/esp/xtensa-esp32-elf/bin


الليد الوامض Blinking LED
 

لأخذ قالب جاهز لمشروع ESP32، فإننا ننسخ مشروع من الأمثلة المتاحة في ESP-IDF وذلك باستخدام التعليمة الخاصة بإنشاء مجلد للمشاريع وثم نسخ المشروع إليه باسم جديد وهو first_app.

mkdir esp32-workspace
cp -r $IDF_PATH/examples/get-started/hello_world esp32-workspace/first_app

ملفات الترويس
 
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

يستخدم الكود تابع من الـFreeRTOS، لذلك يلزمنا الملفين السابقين إضافة إلى الملف التالي والذي يحوي توابع للتعامل مع الدخل/الخرج GPIO.
#include "driver/gpio.h"


 
التابع app_main
 

هذا التابع هو التابع الرئيسي الذي يحوي البرنامج. طالما أن ESP-IDF تستخدم FreeRTOS، فإنه يمكننا إنشاء اجرائية task خاصة بعملية الوميض. يتم إنشاء هذه الإجرائية باستدعاء التابع xTaskCreate. قد لا يكون القارئ مطلعاً على FreeRTOS لذلك كلما أحتجنا لشيء معين منه يتم شرحه في وقته ولنبدأ بـ xTaskCreate والذي له التعريف التالي وأقتبس هنا من دليل FreeRTOS الرسمي reference manual :
BaseType_t xTaskCreate( TaskFunction_t pvTaskCode,
const char * const pcName,
unsigned short usStackDepth,
void *pvParameters,
UBaseType_t uxPriority,
TaskHandle_t *pxCreatedTask );


 
تابع blink_task
 
void blink_task(void *pvParameter)
{
    gpio_pad_select_gpio(BLINK_GPIO);
    gpio_set_direction(BLINK_GPIO, GPIO_MODE_OUTPUT);
    while(1) {
       gpio_set_level(BLINK_GPIO, 0);
       vTaskDelay(1000 / portTICK_PERIOD_MS);
       gpio_set_level(BLINK_GPIO, 1);
       vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
}

إن كود هذا التابع سهل ومباشر كأي كود تعامل مع مداخل/مخارج، حيث يتضمن اختيار رقم الرجل والنمط وسوية الخرج وكل هذا عبر توابع من ESP-IDF


لنرَ الآن المثال كاملاً:
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "driver/gpio.h"
#define BLINK_GPIO 1
void blink_task(void *pvParameter)
{
    gpio_pad_select_gpio(BLINK_GPIO);
    gpio_set_direction(BLINK_GPIO, GPIO_MODE_OUTPUT);
    while(1) {
       gpio_set_level(BLINK_GPIO, 0);
       vTaskDelay(1000 / portTICK_PERIOD_MS);
       gpio_set_level(BLINK_GPIO, 1);
       vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
}
void app_main()
{
    xTaskCreate(&blink_task, "blink_task", configMINIMAL_STACK_SIZE, NULL, 5, NULL);


