## 单元测试

## 基础环境

<b>node: </b>  16.17.1 </br>
<b>react:</b>  18.2.0 </br>
<b>jest:</b>   29.5.0 </br>
<b>@testing-library/react: </b> 14.0.0

### 基础实例

#### hooks测试

##### 只有useState的测试方式

##### 测试window.addEventListener('message', receiveMessage)相关的hooks

待测试代码

```ts
// useReceiveMessage.ts
import { useMemoizedFn } from 'ahooks';
import { useEffect } from 'react';

export enum ReceiveMessageTypes {
  Add = 'add',
  Delete = 'delete',
  Update = 'update',
  Select = 'select',
}

export interface ReceiveMessageData {}

export interface OpenReceivedMessage {
  type: ReceiveMessageTypes;
  data: ReceiveMessageData;
}

export type ReceiveMessageCallbackMap = {
  [key in ReceiveMessageTypes]?: (data: ReceiveMessageData) => void;
};

export const useReciveMessage = (callbackMap?: ReceiveMessageCallbackMap) => {
  const receiveMessage = useMemoizedFn((e: MessageEvent<OpenReceivedMessage>) => {
    const { origin, data: messageInfo } = e;
    if (origin !== window.location.origin && __IsProd__) {
      return;
    }
    const { type, data } = messageInfo;
    callbackMap?.[type]?.(data);
  });

  useEffect(() => {
    window.addEventListener('message', receiveMessage);
    return () => {
      window.addEventListener('message', receiveMessage);
    };
  }, []);
};

```

上述的hooks使用方式：

```ts
import {useReciveMessage, ReceiveMessageTypes} from './useReceiveMessage';

const useHooksComponent = () => {
  const handleAdd = () => {};
  const handleDelete = () => {};

  useReciveMessage({
    [ReceiveMessageTypes.Add]: handleAdd,
    [ReceiveMessageTypes.Delete]: handleDelete,
    // ...
  })
}
```

单元测试

```ts
// useReceiveMessage.test.ts
import { useEffect } from 'react';
import { renderHook, waitFor } from '@testing-library/react';
import { useReciveMessage, OpenReceivedMessage, ReceiveMessageTypes } from '.';

describe('useReciveMessage', () => {
  // spyOn window.addEventListener;
  let mockAddEventListener: jest.SpyInstance;
  let mockRemoveEventListener: jest.SpyInstance;

  beforeEach(() => {
    mockAddEventListener = jest.spyOn(window, 'addEventListener');
    mockRemoveEventListener = jest.spyOn(window, 'removeEventListener');
  });

  afterEach(() => {
    // restore the mock modules.
    mockAddEventListener.mockRestore();
    mockRemoveEventListener.mockRestore();
  });

  it('should add and remove event listener', async () => {
    const callbackMap = {
      [ReceiveMessageTypes.Add]: jest.fn(),
    };

    await renderHook(() => useReciveMessage(callbackMap));

    waitFor(() => {
      expect(mockAddEventListener).toHaveBeenCalledTimes(1);
      expect(mockAddEventListener).toHaveBeenCalledWith('message', expect.any(Function));

      expect(mockRemoveEventListener).toHaveBeenCalledTimes(1);
      expect(mockRemoveEventListener).toHaveBeenCalledWith('message', expect.any(Function));
    });
  });

  it('should call the callback function when receiving a matching message', () => {
    const callbackMap = {
      [ReceiveMessageTypes.Add]: jest.fn(),
    };

    renderHook(() => useReciveMessage(callbackMap));

    const mockMessageEvent: MessageEvent<OpenReceivedMessage> = {
      origin: window.location.origin,
      data: {
        type: ReceiveMessageTypes.Add,
        data: { result: 'example' },
      },
    } as MessageEvent<OpenReceivedMessage>;

    // Get the receiveMessage function
    const receiveMessageHandler = mockAddEventListener.mock.calls[0][1];

    receiveMessageHandler(mockMessageEvent);

    expect(callbackMap[ReceiveMessageTypes.Add]).toHaveBeenCalledTimes(1);
    expect(callbackMap[ReceiveMessageTypes.Add]).toHaveBeenCalledWith({
      result: 'example',
    });
  });

  it('should not call the callback function when receiving a non-matching message', () => {
    const callbackMap = {
      [ReceiveMessageTypes.Unknown]: jest.fn(),
    };

    renderHook(() => useReciveMessage(callbackMap));

    const mockMessageEvent: MessageEvent<OpenReceivedMessage> = {
      origin: window.location.origin,
      data: {
        type: ReceiveMessageTypes.Add,
        data: { result: 'example' },
      },
    } as MessageEvent<OpenReceivedMessage>;

    const receiveMessageHandler = mockAddEventListener.mock.calls[0][1];

    receiveMessageHandler(mockMessageEvent);

    expect(callbackMap[ReceiveMessageTypes.Unknown]).not.toHaveBeenCalled();
  });

  it('should not call the callback function when origin is different in production', () => {
    const callbackMap = {
      [ReceiveMessageTypes.Add]: jest.fn(),
    };

    /**
     * Modify the return value of __IsProd__ in the global object inside jest.config.js
     */
    Object.defineProperty(global, '__IsProd__', {
      value: true,
    });

    renderHook(() => useReciveMessage(callbackMap));

    const mockMessageEvent: MessageEvent<OpenReceivedMessage> = {
      origin: 'https://example.com',
      data: {
        type: ReceiveMessageTypes.Add,
        data: { result: 'example' },
      },
    } as MessageEvent<OpenReceivedMessage>;

    const receiveMessageHandler = mockAddEventListener.mock.calls[0][1];

    receiveMessageHandler(mockMessageEvent);

    expect(callbackMap[ReceiveMessageTypes.Add]).not.toHaveBeenCalled();
  });
});
```

上述实例中的__IsProd__是定义的全局变量，原始代码中来源于const _isProd = process.env.NODE_ENV === 'production';该变量会在.umirc.ts中进行设置全局变量，具体如下：
```ts
import { defineConfig } from 'umi';
export default defineConfig({
  // ...
  define: {
    // ...
      __IsProd__: _isProd
    // ...
  }
  // ...
})

```

但是在jest中如果想要使用全局的变量，则需要在jest.config.js中进行配置，具体如下：

```js
import type { Config } from 'jest';

export default async (): Promise<Config> => {
  return {
    // ...
    global: {
      // ...
      '__IsProd__':  false,
      // ...
    }
    // ...
  }
}

```


##### 测试iframe标签load事件

待测试示例代码如下：

```ts
import Loading from '@/components/loadingbox/Loading';
import { useState } from 'react';
import { useIframe } from './hooks/useIframe';
import { handleReceiveMessage } from './methods';

const Demo = () => {
  const [loading, setLoading] = useState<boolean>(true);

  const { src } = useIframe();

  const onLoaded = () => {
    setLoading(false);
  };

  const receiveMessage = (event) => {
    // receive message and handling
    // ...
    handleReceiveMessage(event);
  }

  useEffect(() => {
    window.addEventListener('message', receiveMessage);
    return () => {
      window.addEventListener('message', receiveMessage);
    };
  }, []);

  return (
    <>
      <iframe onLoad={onLoaded} src={src} frameBorder="0" width={'100%'} height={'100%'} />
      <Loading isShow={loading} className="loading-box"/>
    </>
  );
};

export default Demo;

```

完整的测试方案：

```ts
import { render, waitFor } from '@testing-library/react';
import Demo from '.';
import { useIframe } from './hooks/useIframe';
import { handleReceiveMessage } from './methods';
import React from 'react';

jest.mock('./hooks/useIframe', () => ({ useIframe: jest.fn() }));
jest.mock('./methods', () => ({ handleReceiveMessage: jest.fn() }));

describe('GinAi', () => {
  const mockUseIframe = useIframe as jest.Mock;
  const mockHandleReceiveMessage = handleReceiveMessage as jest.Mock;

  beforeEach(() => {
    jest.resetModules();
    jest.clearAllMocks();
  });

  it('renders iframe and loading component', () => {
    mockUseIframe.mockReturnValue({ src: 'https://example.com' });

    const { container } = render(<GinAi />);

    const iframeElement = container.querySelector('iframe');
    const loadingElement = container.querySelector('.loading-box');

    expect(iframeElement).toBeInTheDocument();
    expect(iframeElement).toHaveAttribute('src', 'https://example.com');
    expect(loadingElement).toBeInTheDocument();
  });

  it('hides loading component after iframe is loaded', () => {
    mockUseIframe.mockReturnValue({ src: 'https://example.com' });
    const setLoadingMock = jest.fn();

    jest.spyOn(React, 'useState').mockReturnValueOnce([true, setLoadingMock]);

    const { container } = render(<GinAi />);

    const iframeElement = container.querySelector('iframe');
    const loadingElement = container.querySelector('.loading-box');

    expect(iframeElement).toBeInTheDocument();
    expect(loadingElement).toBeInTheDocument();

    iframeElement?.dispatchEvent(new Event('load'));

    waitFor(() => {
      expect(setLoadingMock).toHaveBeenCalledWith(false);
      expect(loadingElement).not.toBeInTheDocument();
    });
  });

  it('handles received message of type "InsertTagString"', () => {
    mockUseIframe.mockReturnValue({ src: 'https://example.com' });

    const { container } = render(<GinAi />);

    const iframeElement = container.querySelector('iframe');
    iframeElement?.dispatchEvent(new Event('load'));

    const message = {
      type: 'messageData',
      data: { result: 'example' },
    };

    const messageEvent = new MessageEvent('message', {
      data: { result: 'example' },
      origin: window.location.origin,
    });

    window.dispatchEvent(messageEvent);

    waitFor(() => {
      expect(onMessageInsertTagStringMock).toHaveBeenCalledWith({ result: 'example' });
      expect(mockHandleReceiveMessage).toHaveBeenCalledWith({
        data: { result: 'example' },
        origin: window.location.origin,
      });
    });
  });
});

```


#### 测试过程中遇到的报错信息记录

##### 当待测文件中有import一个图片文件时，出现报错：

待测文件

```tsx
import { FC, memo } from 'react';
import styles from './index.scss';
import emptyDataImg from '@/assets/icon/empty-data.svg';

interface Props {
  text: string;
}

const EmptyData: FC<Props> = function ({ text }) {
  return (
    <div className={styles.noData}>
      <img className="empty-image" src={emptyDataImg} alt="emptyImage" />
      <p className="empty-text">{text}</p>
    </div>
  );
};
export default memo(EmptyData);
```

测试代码如下：

```tsx
import { render } from '@testing-library/react';
import EmptyData from '../index';

describe('EmptyData Component', () => {
  it('renders the component with the provided text', () => {
    const text = 'No data available';
    const { getByText } = render(<EmptyData text={text} />);
    const emptyTextElement = getByText(text);

    expect(emptyTextElement).toBeInTheDocument();
  });
});
```

报错信息如下：

```shell
 ● Test suite failed to run

    Jest encountered an unexpected token

    Jest failed to parse a file. This happens e.g. when your code or its dependencies use non-standard JavaScript syntax, or when Jest is not configured to support such syntax.

    Out of the box Jest supports Babel, which will be used to transform your files into valid JS based on your Babel configuration.

    By default "node_modules" folder is ignored by transformers.

    Here's what you can do:
     • If you are trying to use ECMAScript Modules, see https://jestjs.io/docs/ecmascript-modules for how to enable it.
     • If you are trying to use TypeScript, see https://jestjs.io/docs/getting-started#using-typescript
     • To have some of your "node_modules" files transformed, you can specify a custom "transformIgnorePatterns" in your config.
     • If you need a custom transformation specify a "transform" option in your config.
     • If you simply want to mock your non-JS modules (e.g. binary assets) you can stub them out with the "moduleNameMapper" config option.

    You'll find more details and examples of these config options in the docs:
    https://jestjs.io/docs/configuration
    For information about custom transformations, see:
    https://jestjs.io/docs/code-transformation

    Details:

    /Users/xzl/demo/src/assets/icon/empty-data.svg:1
    ({"Object.<anonymous>":function(module,exports,require,__dirname,__filename,jest){<?xml version="1.0" encoding="UTF-8"?>
                                                                                      ^

    SyntaxError: Unexpected token '<'

      1 | import { FC, memo } from 'react';
      2 | import styles from './index.scss';
    > 3 | import emptyDataImg from '@/assets/icon/empty-data.svg';
        | ^
      4 |
      5 | interface Props {
      6 |   text: string;

      at Runtime.createScriptFromCode (node_modules/jest-runtime/build/index.js:1495:14)
      at Object.<anonymous> (src/components/EmptyData/index.tsx:3:1)
      at Object.<anonymous> (src/components/EmptyData/__test__/index.test.tsx:2:1)

Test Suites: 1 failed, 1 total
Tests:       0 total
Snapshots:   0 total
Time:        107.825 s
Ran all test suites matching /src\/components\/EmptyData/i.
```
<b>解决办法：</b> 这是由于配置问题引起的，是缺少了解析某些文件的插件。需要让jest支持解析文件

具体步骤：

- 引入jest-transform-stub插件
- 在jest.config.ts中transform属性增加配置
  ```ts
  {
    // ....
    transform: {
      '\\.[jt]sx?$': 'ts-jest',
      ".+\\.(svg|css|styl|less|sass|scss|png|jpg|ttf|woff|woff2)$": "jest-transform-stub"
    },
    // ....
  }
  ```
重新启动测试，即可解决这个问题。


