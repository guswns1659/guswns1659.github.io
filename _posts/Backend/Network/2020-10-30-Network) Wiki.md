---
title: "Network) Wiki"
header:
  overlay_image: /assets/spring.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Network
---

# HTTP 메서드 멱등성

## 멱등성이란?
- 연산을 여러 번 적용하더라도 결과가 달라지지 않는 성질을 의미합니다. 즉 메서드가 여러 번 실행되어도, 결과는 같으므로 안전하게 사용할 수 있는 성질이기도 합니다.
- HTTP 메서드 중에서 GET, PUT, DELETE가 멱등성을 유지되어야 한다.
- 삭제를 할 때 해당 id의 엔티티가 없더라도 삭제가 된 것이니 삭제 성공, 실제로 삭제가 이루어져도 삭제성공. 같은 결과를 유지해야 한다. 아래와 같이 네트워크와 통신할 때 발생할 수 있는 에러. 이를 방지하기 위해 멱등성을 유지해야 한다.

![image](https://user-images.githubusercontent.com/55608425/97660832-0a0e2000-1ab6-11eb-9309-c10f791f35ef.png)
- [그림 참고 : 지속 가능한 소프트웨어를 위한 코딩 방법 - 마지막 맺음말.](https://meetup.toast.com/posts/218)

- 아래는 todo를 삭제하는 메서드

```java
public TodoDeleteResponse delete(Long todoId) {
        try {
            Todo foundTodo = todoRepository.findById(todoId).orElseThrow(
                    () -> new NoSuchTodoException("해당 id의 Todo는 없습니다.")
            );
            todoRepository.delete(foundTodo.getId());
            return TodoDeleteResponse.from("삭제 성공");
        } catch (NoSuchTodoException e) {
            return TodoDeleteResponse.from("삭제 성공");
        }
    }
```
