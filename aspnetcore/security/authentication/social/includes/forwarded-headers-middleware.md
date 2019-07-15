## <a name="forward-request-information-with-a-proxy-or-load-balancer"></a>Weiterleiten von Anforderungsinformationen mit einem Proxy oder Lastenausgleich

Wenn die App hinter einem Proxyserver oder Lastenausgleich bereitgestellt wird, können einige der ursprünglichen Anforderungsinformationen im Anforderungsheader an die App weitergeleitet werden. Zu diesen Informationen gehören in der Regel das sichere Anforderungsschema (`https`), den Host und die Client-IP-Adresse. Apps lesen diese Anforderungsheader nicht automatisch, um die ursprünglichen Anforderungsinformationen zu ermitteln und zu verwenden.

Das Schema wird bei der Linkgenerierung verwendet, die den Authentifizierungsflow bei externen Anbietern betrifft. Der Verlust des sicheren Schemas (`https`) führt dazu, dass die App falsche unsichere Umleitungs-URLs generiert.

Verwenden Sie Middleware für weitergeleitete Header, um der App zur Anforderungsverarbeitung die Informationen der ursprünglichen Anforderung verfügbar zu machen.

Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.
