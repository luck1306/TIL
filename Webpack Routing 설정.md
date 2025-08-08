created at : 20250711 12:16

"react-router-dom"으로 routing 기능을 구현할 때 고려 사항

공리 :
- react는 SPA(Single Page Application)이다.
	- 따라서 경로는 react에서 처리된다.
- "http://localhost:3000/about" 꼴의 요청 시 자연스럽게 react앱의 about.html을 조회한다.

이때, Webpack을 사용 중이라면 위 상황을 조정할 수 있는 설정이 있다.
_참조: <a href="https://webpack.kr/configuration/dev-server/#devserverhistoryapifallback">https://webpack.kr/configuration/dev-server/#devserverhistoryapifallback</a>_

devServer.histroyApiFallback 설정으로 기본값은 false이다.
해당 값을 true로 변경하면 모든 NotFound 요청에 대해 index.html을 반환하는데, react를 렌더링하며 react-router-dom의 Route에 등록된 경로라면 내부적으로 해당하는 컴포넌트를 렌더링한다.