## 7.4. Browser credentials handling                  

<div class="abstract">
	<p>이 절에서는 Neo4j의 인증 및 권한 부여에 대해서 설명합니다. 
	</p>
</div>
This section explains how to control how credentials are handled in Neo4j Browser.

Neo4j Browser has two mechanisms for avoiding users having to repeatedly enter their Neo4j credentials.

First, while the Browser is open in a web browser tab, it ensures that the existing database session is kept alive.            This is subject to a timeout.            The timeout is configured in the setting `browser.credential_timeout`.            The timeout is reset whenever there is user interaction with the Browser.         

Second, the Browser can also cache the user’s Neo4j credentials locally.            When credentials are cached, they are stored unencrypted in the web browser’s local storage.            If the web browser tab is closed and then re-opened, the session is automatically re-established using the cached credentials.            This local storage is also subject to the timeout configured in the setting `browser.credential_timeout`.            In addition, caching credentials in browser local storage can be disabled altogether.            To disable credentials caching, set `browser.retain_connection_credentials=false` in the server configuration.         

If the user issues a `:server disconnect` command then any existing session is terminated and the credentials are cleared from local storage.         