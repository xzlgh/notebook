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





