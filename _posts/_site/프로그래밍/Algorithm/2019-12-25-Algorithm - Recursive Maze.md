츌처 : [KOCW 부경대학교 권오흠 교수 알고리즘 강의 참고](http://www.kocw.net/home/search/kemView.do?kemId=1148815)


## 목차
- 미로찾기 재귀적 사고
- Psudocode
## 핵심
- **연습! 연습!**

## 미로찾기 재귀적 사고
현재 위치에서 출구까지 가는 경로가 있으려면
1. 현재 좌표가 출구거나
2. 이웃한 셀들 중 하나에서 현재 위치를 지나지 않고 출구로 가는 방법이 있거나.

## Psudocode
```java
    boolean findPath(x, y) {
        if (x,y) is either on the wall or a visited cell
            return false;
        else if (x,y) is the exit
            return true;
        else
            mark (x,y) as a visited cell;
            for each neighbouring cell (x', y') of (x,y) do
                if findPath(x', y')
                    return true;
            return false;
    }
```
## code
[실제 코드는 여기로](https://github.com/guswns1659/Writing/blob/master/TIL/kr/Algorithm/Maze.java)