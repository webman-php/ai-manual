# 事件列表 及 数据结构

## user.register
* 含义  
  用户注册时触发
* 数据结构  
`plugin\user\app\model\User` 实例


示例
```php
<?php
use plugin\user\app\model\User;
return [
    'user.register' => [
        function (User $user) {
            echo $user->id;
            echo $user->username;
            echo $user->nickname;
            echo $user->avatar;
        }
    ]
];
```

## user.login
* 含义  
  用户登录时触发
* 数据结构  
  `plugin\user\app\model\User` 实例

## user.logout
* 数据结构  
  `plugin\user\app\model\User` 实例

## ai.menu.list
* 含义  
  渲染左侧图标菜单时触发
* 数据结构  
`plugin\ai\app\event\data\EventData` 实例

示例
```php
<?php
use plugin\ai\app\event\data\EventData;
return [
    // 渲染左侧图标菜单时触发
    'ai.menu.list' => [
        function (EventData $object) {
            // $data里是所有的菜单数据 
            $data = $object->data;
            // 添加一条invite图标菜单
            $data['invite'] = [
                'enabled' => true, // 是否启用该菜单
                'title' => '邀请好友', // 菜单标题
                'icon' => [
                    'light' => '<i class="bi bi-share"></i>', // 明亮主题下的图标
                    'dark' => '<i class="bi bi-share"></i>',  // 暗黑主题下的图标
                    'active' => '<i class="bi bi-share-fill"></i>' // 被选中后的图标
                ],
                'url' => '/app/foo', // iframe url 地址
                'mobile' => true, // 是否在移动端显示图标菜单
            ];
            $object->data = $data;
        }
    ]
];
```

## ai.payment.success
* 含义  
  支付成功时触发
* 数据结构  
  `plugin\ai\app\event\data\PaymentData` 实例

示例
```php
<?php
use plugin\ai\app\event\data\PaymentData;
return [
    'ai.payment.success' => function (PaymentData $paymentData) {
        var_export($paymentData->userId);
        var_export($paymentData->data);
    }
];
```

打印类似
```php
1066

array (
  'plan' => 1,
  'price' => 29,
  'months' => 1,
  'name' => '月度会员',
  'gpt3' => 3000,
  'gpt4' => 100,
  'ernie' => 3000,
  'qwen' => 3000,
  'spark' => 3000,
  'gemini' => 3000,
  'chatglm' => 3000,
  'midjourney' => 100,
)
````


## ai.model.handler.dispatch
* 含义  
  大模型请求时，分发处理器前触发
* 数据结构  
 `plugin\ai\app\event\data\HandlerData` 实例

示例
```php
<?php
use plugin\ai\app\event\data\HandlerData;
return [
    'ai.model.handler.dispatch' => function (HandlerData $handlerData) {
        var_export($handlerData->options);
        var_export($handlerData->data);
        var_export($handlerData->handler);
        // 将所有大模型请求交给Gpt4处理
        $handlerData->handler = plugin\ai\app\handler\Gpt4::class;
    }
];
```

打印类似
```php
array (
  'stream' =>
  \Closure::__set_state(array(
  )),
  'complete' =>
  \Closure::__set_state(array(
  )),
)

array (
  'model' => 'gpt-3.5-turbo',
  'stream' => true,
  'messages' =>
  array (
    0 =>
    array (
      'role' => 'system',
      'content' => '你是一个智能助手',
    ),
    1 =>
    array (
      'role' => 'assistant',
      'content' => '你好，我是AI助手，请问您需要什么帮助？',
    ),
    2 =>
    array (
      'role' => 'user',
      'content' => 'hello',
    ),
  ),
  'temperature' => 0.5,
)

'plugin\\ai\\app\\handler\\Gpt3'
```

options是请求的回调函数，stream代表收到流式响应时触发，complete代表响应完毕时触发。原型类似如下：

```php
[
    'stream' => function ($data) {
        var_export($data);
    },
    'complete' => function ($result, $response) {
        var_export($result);
    }
]
```

data是请求的参数

handler是处理器类名

以上参数都可以进行修改，达到介入请求处理的目的

## ai.chat.completions.request
* 含义  
  聊天类大模型请求时触发

示例
```php
<?php
use plugin\ai\app\event\data\ModelRequestData;
return [
    'ai.chat.completions.request' => function (ModelRequestData $modelRequestData) {
        var_export($modelRequestData->options);
        var_export($modelRequestData->data);
        // 如果请求模型是gpt-3.5，将其转换为gpt-4
        if ($modelRequestData->data['model'] === 'gpt-3.5') {
            $modelRequestData->data['model'] = 'gpt-4';
        }
    }
];
```

打印类似
```php
array (
  'stream' =>
  \Closure::__set_state(array(
  )),
  'complete' =>
  \Closure::__set_state(array(
  )),
)

array (
  'model' => 'gpt-4',
  'stream' => true,
  'messages' =>
  array (
    0 =>
    array (
      'role' => 'assistant',
      'content' => '你好，我是AI助手，请问您需要什么帮助？',
    ),
    1 =>
    array (
      'role' => 'user',
      'content' => 'hello
',
    ),
  ),
  'temperature' => 0.5,
)
```

## ai.chat.completions.response
* 含义  
  聊天类大模型响应时触发
* 数据结构
  `plugin\ai\app\event\data\ModelResponseData` 实例

示例
```php
<?php
use plugin\ai\app\event\data\ModelResponseData;
return [
    'ai.chat.completions.response' => function (ModelResponseData $responseData) {
        var_export($responseData->modelRequestData);
        // $responseData->data 如果是字符串，则代表返回的数据内容，如果是数组，则代表发送错误
        var_export($responseData->data);
    }
];
```

打印类似
```php
\plugin\ai\app\event\data\ModelRequestData::__set_state(array(
   'data' =>
  array (
    'model' => 'gpt-4',
    'stream' => true,
    'messages' =>
    array (
      0 =>
      array (
        'role' => 'system',
        'content' => '你是一个智能助手',
      ),
      1 =>
      array (
        'role' => 'assistant',
        'content' => '你好，我是AI助手，请问您需要什么帮助？',
      ),
      2 =>
      array (
        'role' => 'user',
        'content' => 'hello',
      ),
    ),
    'temperature' => 0.5,
  ),
   'options' =>
  array (
    'stream' =>
    \Closure::__set_state(array(
    )),
    'complete' =>
    \Closure::__set_state(array(
    )),
  ),
))

// 如果成功代表返回的数据内容
'Hello! How can I assist you today?'

// 如果是错误打印类似
array (
  'error' =>
  array (
    'message' => 'xxx',
    'type' => 'xxx',
  ),
)
```

## ai.image.generations.request
* 含义  
  图片生成类大模型请求时触发 例如Dall-e (Midjourney属于任务类，不会触发此事件，见下面文档)
* 数据结构
  `plugin\ai\app\event\data\ModelRequestData` 实例

示例
```php
<?php
use plugin\ai\app\event\data\ModelRequestData;
return [
    'ai.image.generations.request' => function (ModelRequestData $modelRequestData) {
        var_export($modelRequestData->options);
        var_export($modelRequestData->data);
    }
];
```

打印类似
```php
array (
  'complete' =>
  \Closure::__set_state(array(
  )),
)

array (
  'model' => 'dall.e',
  'prompt' => '狗',
  'n' => 1,
  'size' => '1024x1024',
)
```

## ai.image.generations.response
* 含义  
  图片生成类大模型响应时触发 例如Dall-e (Midjourney属于任务类，不会触发此事件，见下面文档)
* 数据结构
  `plugin\ai\app\event\data\ModelResponseData` 实例

示例
```php
<?php
use plugin\ai\app\event\data\ModelResponseData;
return [
    'ai.image.generations.response' => function (ModelResponseData $responseData) {
        var_export($responseData->modelRequestData);
        // $responseData->data 为数组
        var_export($responseData->data);
    }
];
```

打印类似
```php
\plugin\ai\app\event\data\ModelRequestData::__set_state(array(
   'data' =>
  array (
    'model' => 'dall.e',
    'prompt' => '狗',
    'n' => 1,
    'size' => '1024x1024',
  ),
   'options' =>
  array (
    'complete' =>
    \Closure::__set_state(array(
    )),
  ),
))

array (
  'created' => 1713958340,
  'data' =>
  array (
    0 =>
    array (
      'revised_prompt' => 'A beautiful scene of a playful dog in a lush green park. The dog, boasting a glossy brown coat, energetically chases a brightly colored ball. The park surrounding the dog is vibrantly green with beautiful flowers scattered around. The sun shines brightly in the blue sky, casting playful shadows on the ground.',
      'url' => 'https://dalleproduse.blob.core.windows.net/private/images/0acac2d3-3951-4e60-a18b-ae717db0be22/generated_00.png?se=2024-04-25T11%3A32%3A35Z&sig=IX4p2s6lAG6yDqc6hJd8RGARfofWPcVpzemLDl615Lw%3D&ske=2024-04-30T21%3A52%3A45Z&skoid=09ba021e-c417-441c-b203-c81e5dcd7b7f&sks=b&skt=2024-04-23T21%3A52%3A45Z&sktid=33e01921-4d64-4f8c-a055-5bdaffd5e33d&skv=2020-10-02&sp=r&spr=https&sr=b&sv=2020-10-02',
    ),
  ),
)
```

## ai.task.imagine.request
* 含义  
  任务类大模型画图任务请求时触发 例如 Midjourney画图
* 数据结构
  `plugin\ai\app\event\data\ModelRequestData` 实例

示例
```php
<?php
use plugin\ai\app\event\data\ModelRequestData;
return [
    'ai.task.imagine.request' => function (ModelRequestData $modelRequestData) {
        var_export($modelRequestData->options);
        var_export($modelRequestData->data);
    }
];
```

打印类似
```php
array (
  'complete' =>
  \Closure::__set_state(array(
  )),
)

array (
  'model' => 'midjourney',
  'images' =>
  array (
  ),
  'notifyUrl' => 'http://127.0.0.1:8787/app/ai/task/notify',
  'prompt' => 'cat --v 6.0 --relax',
  'allowFast' => false,
)
```

## ai.task.imagine.response
* 含义  
  任务类大模型画图任务响应时触发 例如 Midjourney画图
* 数据结构
  `plugin\ai\app\event\data\ModelResponseData` 实例

示例
```php
<?php
use plugin\ai\app\event\data\ModelResponseData;
return [
    'ai.task.imagine.response' => function (ModelResponseData $responseData) {
        var_export($responseData->modelRequestData);
        // $responseData->data 为数组
        var_export($responseData->data);
    }
];
```

打印类似
```php
\plugin\ai\app\event\data\ModelRequestData::__set_state(array(
   'data' =>
  array (
    'model' => 'midjourney',
    'images' =>
    array (
    ),
    'notifyUrl' => 'http://127.0.0.1:8787/app/ai/task/notify',
    'prompt' => 'cat --v 6.0 --relax',
    'allowFast' => false,
  ),
   'options' =>
  array (
    'complete' =>
    \Closure::__set_state(array(
    )),
  ),
))
array (
  'code' => 0,
  'msg' => 'ok',
  'taskId' => '1713960408131596202',
  'data' =>
  array (
  ),
)
```

## ai.task.action.request
* 含义  
  任务类大模型图片操作请求时触发 例如 Midjourney任务选图、变换等
* 数据结构
  `plugin\ai\app\event\data\ModelRequestData` 实例

示例
```php
<?php
use plugin\ai\app\event\data\ModelRequestData;
return [
    'ai.task.action.request' => function (ModelRequestData $modelRequestData) {
        var_export($modelRequestData->options);
        var_export($modelRequestData->data);
    }
];
```

打印类似
```php
array (
  'complete' =>
  \Closure::__set_state(array(
  )),
)
array (
  'model' => 'midjourney',
  'taskId' => '1713960408131596202',
  'customId' => 'MJ::JOB::upsample::3::fb4b8cfa-2be7-4ae8-9013-1234567',
  'prompt' => NULL,
  'mask' => NULL,
  'allowFast' => false,
  'notifyUrl' => 'http://127.0.0.1:8787/app/ai/task/notify',
)
```

## ai.task.action.response
* 含义  
  任务类大模型图片操作响应时触发 例如 Midjourney任务选图、变换等
* 数据结构
  `plugin\ai\app\event\data\ModelResponseData` 实例

示例
```php
<?php
use plugin\ai\app\event\data\ModelResponseData;
return [
    'ai.task.action.response' => function (ModelResponseData $responseData) {
        var_export($responseData->modelRequestData);
        // $responseData->data 为数组
        var_export($responseData->data);
    }
];
```

打印类似
```php
\plugin\ai\app\event\data\ModelRequestData::__set_state(array(
   'data' =>
  array (
    'model' => 'midjourney',
    'taskId' => '1713960408131596202',
    'customId' => 'MJ::JOB::upsample::2::fb4b8cfa-2be7-4ae8-9013-123456',
    'prompt' => NULL,
    'mask' => NULL,
    'allowFast' => false,
    'notifyUrl' => 'http://127.0.0.1:8787/app/ai/task/notify',
  ),
   'options' =>
  array (
    'complete' =>
    \Closure::__set_state(array(
    )),
  ),
))
array (
  'code' => 0,
  'msg' => 'ok',
  'taskId' => '1713960713762262689',
  'data' =>
  array (
  ),
)
```

## ai.task.status.request
* 含义  
  任务类大模型任务状态请求时触发 例如 Midjourney任务状态查询
* 数据结构
  `plugin\ai\app\event\data\ModelRequestData` 实例

示例
```php
<?php
use plugin\ai\app\event\data\ModelRequestData;
return [
    'ai.task.status.request' => function (ModelRequestData $modelRequestData) {
        var_export($modelRequestData->options);
        var_export($modelRequestData->data);
    }
];
```

打印类似
```php
array (
  'complete' =>
  \Closure::__set_state(array(
  )),
)
array (
  'model' => 'midjourney',
  'taskId' => '1713960907174341346',
)
```

## ai.task.status.response
* 含义  
  任务类大模型任务状态响应时触发 例如 Midjourney任务状态查询
* 数据结构
  `plugin\ai\app\event\data\ModelResponseData` 实例

示例
```php
<?php
use plugin\ai\app\event\data\ModelResponseData;
return [
    'ai.task.status.response' => function (ModelResponseData $responseData) {
        var_export($responseData->modelRequestData);
        var_export($responseData->data);
        $$data = $responseData->data;
        // 图片进度
        $progress = $data['data']['progress'] ?? '';
        // mj返回的原始url地址
        $url = $data['data']['imageRawUrl'] ?? ''; 
        // 图片进度100%则替换图片地址域名部分，让其可以被国内访问 https://cdn.imgin.top 是一个mj图片代理
        if ($progress === '100%' && $url) {
            $data['data']['imageUrl'] = str_replace('https://cdn.discordapp.com', 'https://cdn.imgin.top', $url);
            $responseData->data = $data;
            // 返回false代表事件到此结束，不再执行 ai.task.status.response 的其它事件函数
            // 包括 plugin/ai/config/event.php 中定义的 ai.task.status.response 事件函数不会再执行
            return false;
        }
    }
];
```

打印类似
```php
\plugin\ai\app\event\data\ModelRequestData::__set_state(array(
   'data' =>
  array (
    'model' => 'midjourney',
    'taskId' => '1713961086218383903',
  ),
   'options' =>
  array (
    'complete' =>
    \Closure::__set_state(array(
    )),
  ),
))

array (
  'code' => 0,
  'msg' => 'success',
  'data' =>
  array (
    'id' => '1713961086218383903',
    'action' => 'UPSCALE',
    'status' => 'FINISHED',
    'submitTime' => 1713961086,
    'startTime' => 1713961086,
    'finishTime' => 1713961087,
    'progress' => '100%',
    'imageUrl' => 'https://cdn.imgin.top/attachments/1148151204875075657/1232666884860547194/imgind_cat_39d7cf9f-c2f0-4adf-a124-0020fb9f867f.png?ex=662a49ff&is=6628f87f&hm=a378497666bf451f80564587869cc96b551347df7fce375f5cae5acb678dbb8e&',
    'imageRawUrl' => 'https://cdn.imgin.top/attachments/1148151204875075657/1232666884860547194/imgind_cat_39d7cf9f-c2f0-4adf-a124-0020fb9f867f.png?ex=662a49ff&is=6628f87f&hm=a378497666bf451f80564587869cc96b551347df7fce375f5cae5acb678dbb8e&',
    'prompt' => 'cat --v 6.0 --relax',
    'finalPrompt' => 'cat --v 6.0',
    'params' =>
    array (
      'customId' => 'MJ::JOB::upsample::1::fb4b8cfa-2be7-4ae8-9013-8d0cf7f27390',
      'messageId' => '1232664428378591342',
      'index' => '1',
    ),
    'images' =>
    array (
    ),
    'attachments' =>
    array (
    ),
    'description' => NULL,
    'failReason' => NULL,
    'messageHash' => '39d7cf9f-c2f0-4adf-a124-0020fb9f867f',
    'nonce' => '1713961086218123456',
    'messageId' => '123266688516123456',
    'discordId' => '114815120487123456',
    'data' =>
    array (
      'uid' => 1713961086,
    ),
    'buttons' =>
    array (
      0 =>
      array (
        0 =>
        array (
          'type' => 2,
          'style' => 2,
          'label' => 'Upscale (Subtle)',
          'emoji' =>
          array (
            'name' => 'upscale_1',
            'id' => '940147353879478324',
          ),
          'custom_id' => 'MJ::JOB::upsample_v6_2x_subtle::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
        ),
        1 =>
        array (
          'type' => 2,
          'style' => 2,
          'label' => 'Upscale (Creative)',
          'emoji' =>
          array (
            'name' => 'upscale_1',
            'id' => '940147353879478324',
          ),
          'custom_id' => 'MJ::JOB::upsample_v6_2x_creative::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
        ),
        2 =>
        array (
          'type' => 2,
          'style' => 2,
          'label' => 'Vary (Subtle)',
          'emoji' =>
          array (
            'name' => '🪄',
          ),
          'custom_id' => 'MJ::JOB::low_variation::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
        ),
        3 =>
        array (
          'type' => 2,
          'style' => 2,
          'label' => 'Vary (Strong)',
          'emoji' =>
          array (
            'name' => '🪄',
          ),
          'custom_id' => 'MJ::JOB::high_variation::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
        ),
        4 =>
        array (
          'type' => 2,
          'style' => 2,
          'label' => 'Vary (Region)',
          'emoji' =>
          array (
            'name' => '🖌️',
          ),
          'custom_id' => 'MJ::Inpaint::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
        ),
      ),
      1 =>
      array (
        0 =>
        array (
          'type' => 2,
          'style' => 2,
          'label' => 'Zoom Out 2x',
          'emoji' =>
          array (
            'name' => '🔍',
          ),
          'custom_id' => 'MJ::Outpaint::50::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
        ),
        1 =>
        array (
          'type' => 2,
          'style' => 2,
          'label' => 'Zoom Out 1.5x',
          'emoji' =>
          array (
            'name' => '🔍',
          ),
          'custom_id' => 'MJ::Outpaint::75::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
        ),
        2 =>
        array (
          'type' => 2,
          'style' => 2,
          'label' => 'Custom Zoom',
          'emoji' =>
          array (
            'name' => '🔍',
          ),
          'custom_id' => 'MJ::CustomZoom::39d7cf9f-c2f0-4adf-a124-0020fb9f867f',
        ),
      ),
      2 =>
      array (
        0 =>
        array (
          'type' => 2,
          'style' => 2,
          'emoji' =>
          array (
            'name' => '⬅️',
          ),
          'custom_id' => 'MJ::JOB::pan_left::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
        ),
        1 =>
        array (
          'type' => 2,
          'style' => 2,
          'emoji' =>
          array (
            'name' => '➡️',
          ),
          'custom_id' => 'MJ::JOB::pan_right::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
        ),
        2 =>
        array (
          'type' => 2,
          'style' => 2,
          'emoji' =>
          array (
            'name' => '⬆️',
          ),
          'custom_id' => 'MJ::JOB::pan_up::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
        ),
        3 =>
        array (
          'type' => 2,
          'style' => 2,
          'emoji' =>
          array (
            'name' => '⬇️',
          ),
          'custom_id' => 'MJ::JOB::pan_down::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
        ),
      ),
    ),
  ),
)
```

## ai.task.notify
* 含义  
  任务类大模型任务状态通知时触发 例如 Midjourney任务状态通知。
* 数据结构
  `plugin\ai\app\event\data\EventData` 实例

示例
```php
<?php
use plugin\ai\app\event\data\EventData;
return [
    'ai.task.notify' => function (EventData $object) {
        var_export($object->data);
    }
];
```

打印类似
```php
array (
  'id' => '1713961086218383903',
  'action' => 'UPSCALE',
  'status' => 'FINISHED',
  'submitTime' => 1713961086,
  'startTime' => 1713961086,
  'finishTime' => 1713961087,
  'progress' => '100%',
  'imageUrl' => 'https://cdn.imgin.top/attachments/1148151204875075657/1232666884860547194/imgind_cat_39d7cf9f-c2f0-4adf-a124-0020fb9f867f.png?ex=662a49ff&is=6628f87f&hm=a378497666bf451f80564587869cc96b551347df7fce375f5cae5acb678dbb8e&',
  'imageRawUrl' => 'https://cdn.discordapp.com/attachments/1148151204875075657/1232666884860547194/imgind_cat_39d7cf9f-c2f0-4adf-a124-0020fb9f867f.png?ex=662a49ff&is=6628f87f&hm=a378497666bf451f80564587869cc96b551347df7fce375f5cae5acb678dbb8e&',
  'prompt' => 'cat --v 6.0 --relax',
  'finalPrompt' => 'cat --v 6.0',
  'params' =>
  array (
    'customId' => 'MJ::JOB::upsample::1::fb4b8cfa-2be7-4ae8-9013-8d0cf7f27390',
    'messageId' => '1232664428378591342',
    'index' => '1',
  ),
  'images' =>
  array (
  ),
  'attachments' =>
  array (
  ),
  'description' => NULL,
  'failReason' => NULL,
  'messageHash' => '39d7cf9f-c2f0-4adf-a124-0020fb9f867f',
  'nonce' => '1713961086218383903',
  'messageId' => '1232666885166989353',
  'discordId' => '1148151204875075657',
  'data' =>
  array (
    'uid' => 1713961086,
  ),
  'buttons' =>
  array (
    0 =>
    array (
      0 =>
      array (
        'type' => 2,
        'style' => 2,
        'label' => 'Upscale (Subtle)',
        'emoji' =>
        array (
          'name' => 'upscale_1',
          'id' => '940147353879478324',
        ),
        'custom_id' => 'MJ::JOB::upsample_v6_2x_subtle::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
      ),
      1 =>
      array (
        'type' => 2,
        'style' => 2,
        'label' => 'Upscale (Creative)',
        'emoji' =>
        array (
          'name' => 'upscale_1',
          'id' => '940147353879478324',
        ),
        'custom_id' => 'MJ::JOB::upsample_v6_2x_creative::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
      ),
      2 =>
      array (
        'type' => 2,
        'style' => 2,
        'label' => 'Vary (Subtle)',
        'emoji' =>
        array (
          'name' => '🪄',
        ),
        'custom_id' => 'MJ::JOB::low_variation::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
      ),
      3 =>
      array (
        'type' => 2,
        'style' => 2,
        'label' => 'Vary (Strong)',
        'emoji' =>
        array (
          'name' => '🪄',
        ),
        'custom_id' => 'MJ::JOB::high_variation::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
      ),
      4 =>
      array (
        'type' => 2,
        'style' => 2,
        'label' => 'Vary (Region)',
        'emoji' =>
        array (
          'name' => '🖌️',
        ),
        'custom_id' => 'MJ::Inpaint::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
      ),
    ),
    1 =>
    array (
      0 =>
      array (
        'type' => 2,
        'style' => 2,
        'label' => 'Zoom Out 2x',
        'emoji' =>
        array (
          'name' => '🔍',
        ),
        'custom_id' => 'MJ::Outpaint::50::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
      ),
      1 =>
      array (
        'type' => 2,
        'style' => 2,
        'label' => 'Zoom Out 1.5x',
        'emoji' =>
        array (
          'name' => '🔍',
        ),
        'custom_id' => 'MJ::Outpaint::75::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
      ),
      2 =>
      array (
        'type' => 2,
        'style' => 2,
        'label' => 'Custom Zoom',
        'emoji' =>
        array (
          'name' => '🔍',
        ),
        'custom_id' => 'MJ::CustomZoom::39d7cf9f-c2f0-4adf-a124-0020fb9f867f',
      ),
    ),
    2 =>
    array (
      0 =>
      array (
        'type' => 2,
        'style' => 2,
        'emoji' =>
        array (
          'name' => '⬅️',
        ),
        'custom_id' => 'MJ::JOB::pan_left::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
      ),
      1 =>
      array (
        'type' => 2,
        'style' => 2,
        'emoji' =>
        array (
          'name' => '➡️',
        ),
        'custom_id' => 'MJ::JOB::pan_right::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
      ),
      2 =>
      array (
        'type' => 2,
        'style' => 2,
        'emoji' =>
        array (
          'name' => '⬆️',
        ),
        'custom_id' => 'MJ::JOB::pan_up::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
      ),
      3 =>
      array (
        'type' => 2,
        'style' => 2,
        'emoji' =>
        array (
          'name' => '⬇️',
        ),
        'custom_id' => 'MJ::JOB::pan_down::1::39d7cf9f-c2f0-4adf-a124-0020fb9f867f::SOLO',
      ),
    ),
  ),
);
```
