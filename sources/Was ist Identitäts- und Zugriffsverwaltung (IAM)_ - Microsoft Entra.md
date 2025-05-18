---
title: Was ist Identitäts- und Zugriffsverwaltung (IAM)? - Microsoft Entra
---

# Was ist Identitäts- und Zugriffsverwaltung (IAM)? - Microsoft Entra

url:: https://learn.microsoft.com/de-de/entra/fundamentals/introduction-identity-access-management
up: [[sources]]

#source

In diesem Artikel lernen Sie einige der grundlegenden Konzepte der Identitäts- und Zugriffsverwaltung (IAM) kennen und erfahren, warum sie wichtig ist und wie sie funktioniert.

Die Identitäts- und Zugriffsverwaltung stellt sicher, dass die richtigen Personen, Computer und Softwarekomponenten zur richtigen Zeit Zugriff auf die richtigen Ressourcen erhalten. Zunächst beweist die Person, der Computer oder die Softwarekomponente, das zu sein, für das sie/er sich ausgibt. Anschließend wird der Person, dem Computer oder der Softwarekomponente der Zugriff auf oder die Verwendung bestimmter Ressourcen erlaubt oder verweigert.

Informationen zu den grundlegenden Begriffen und Konzepten finden Sie unter [Identitätsgrundlagen](https://learn.microsoft.com/de-de/entra/fundamentals/identity-fundamental-concepts).

## Wozu dient IAM?

IAM-Systeme bieten in der Regel die folgenden Kernfunktionen:

* **Identitätsverwaltung**: Die Erstellung, Speicherung und Verwaltung von Identitätsinformationen. Identitätsanbieter (Identity Provider, IdP) sind Softwarelösungen, die zum Nachverfolgen und Verwalten von Benutzeridentitäten sowie der mit diesen Identitäten verbundenen Berechtigungen und Zugriffsebenen verwendet werden.

* **Identitätsverbund**: Sie können Benutzern, die bereits über Kennwörter an anderer Stelle verfügen (z. B. in Ihrem Unternehmensnetzwerk oder bei einem Internetdienstanbieter oder einem sozialen Netzwerk als Identitätsanbieter), den Zugriff auf Ihr System ermöglichen.

* **Bereitstellen und Aufheben der Bereitstellung von Benutzern**: Die Erstellung und Verwaltung von Benutzerkonten, bei dem unter anderem festgelegt wird, welche Benutzer Zugriff auf welche Ressourcen haben, und bei dem Berechtigungen und Zugriffsebenen zugewiesen werden.

* **Authentifizierung von Benutzern**: Authentifizieren Sie einen Benutzer, einen Computer oder eine Softwarekomponente, indem Sie bestätigen, dass sie das sind, wofür er/sie sich ausgibt. Sie können die Multi-Faktor-Authentifizierung (MFA) für einzelne Benutzer für zusätzliche Sicherheit oder einmaliges Anmelden (Single Sign-On, SSO) hinzufügen, um Benutzern die Authentifizierung ihrer Identität mit einem Portal anstelle von vielen verschiedenen Ressourcen zu ermöglichen.

* **Autorisierung von Benutzern**: Mit der Autorisierung wird sichergestellt, dass einem Benutzer genau die Ebene und die Art des Zugriffs auf ein Tool gewährt wird, zu denen er berechtigt ist. Benutzer können auch in Gruppen oder Rollen unterteilt werden, sodass großen Kohorten von Benutzern dieselben Berechtigungen gewährt werden können.

* **Zugriffssteuerung**: Der Prozess, bei dem festgelegt wird, wer oder was Zugriff auf welche Ressourcen hat. Dies umfasst das Definieren von Benutzerrollen und Berechtigungen sowie das Einrichten von Authentifizierungs- und Autorisierungsmechanismen. Zugriffssteuerungen regeln den Zugriff auf Systeme und Daten.

* **Berichte und Überwachung**: Generieren Sie Berichte nach der Ausführung von Aktionen auf der Plattform (z. B. Anmeldezeit, Systeme, auf die zugegriffen wurde, und Authentifizierungstyp), um die Compliance sicherzustellen und Sicherheitsrisiken zu bewerten. Gewinnen Sie Einblicke in die Sicherheit und die Verwendungsmuster in Ihrer Umgebung.

## Funktionsweise von IAM

Dieser Abschnitt bietet eine Übersicht über den Authentifizierungs- und Autorisierungsprozess und die gängigeren Standards.

Angenommen, Sie haben eine Anwendung, die einen Benutzer anmeldet und dann auf eine geschützte Ressource zugreift.

![Diagramm, das den Prozess der Benutzerauthentifizierung und -autorisierung für den Zugriff auf eine geschützte Ressource mithilfe eines Identitätsanbieters zeigt.](https://learn.microsoft.com/de-de/entra/fundamentals/media/introduction-identity-access-management/openid-oauth.svg)

1. Der Benutzer ( Ressourcenbesitzer) leitet von der Clientanwendung aus eine Authentifizierungsanforderung an den Identitätsanbieter/Autorisierungsserver ein.

2. Wenn die Anmeldeinformationen gültig sind, sendet der Identitätsanbieter/Autorisierungsserver zuerst ein ID-Token mit Informationen zum Benutzer zurück an die Clientanwendung.

3. Der Identitätsanbieter/Autorisierungsserver holt außerdem die Zustimmung des Endbenutzers ein und erteilt der Clientanwendung die Autorisierung für den Zugriff auf die geschützte Ressource. Die Autorisierung wird in einem Zugriffstoken erteilt, das ebenfalls an die Clientanwendung zurückgesendet wird.

4. Das Zugriffstoken wird an nachfolgende Anforderungen angefügt, die von der Clientanwendung an den geschützten Ressourcenserver gesendet werden.

5. Der Identitätsanbieter/Autorisierungsserver überprüft das Zugriffstoken. Bei Erfolg wird die Anforderung für geschützte Ressourcen erteilt, und eine Antwort wird an die Clientanwendung zurückgesendet.

Weitere Informationen finden Sie unter [Authentifizierung und Autorisierung](https://learn.microsoft.com/de-de/entra/fundamentals/../identity-platform/authentication-vs-authorization#authentication-and-authorization-using-the-microsoft-identity-platform).

### Authentifizierungs- und Autorisierungsstandards

Dies sind die bekanntesten und am häufigsten verwendeten Authentifizierungs- und Autorisierungsstandards:

#### OAuth 2.0

OAuth ist ein Identitätsverwaltungsprotokoll mit offenen Standards, das einen sicheren Zugriff für Websites, mobile Apps sowie das Internet der Dinge und andere Geräte bietet. Es verwendet Token, die während der Übertragung verschlüsselt werden und macht die gemeinsame Nutzung von Zugangsdaten überflüssig. OAuth 2.0, die neueste Version von OAuth, ist ein beliebtes Framework, das von großen Social-Media-Plattformen und Verbraucherdiensten verwendet wird, von Facebook und LinkedIn bis hin zu Google, PayPal und Netflix. Weitere Informationen finden Sie unter [OAuth 2.0-Protokoll](https://learn.microsoft.com/de-de/entra/fundamentals/../identity-platform/v2-protocols).

#### OpenID Connect (OIDC)

Mit der Veröffentlichung von OpenID Connect (Verschlüsselung mit öffentlichen Schlüsseln) wurde OpenID zu einer weit verbreiteten Authentifizierungsebene für OAuth. Wie SAML wird OpenID Connect (OIDC) häufig für einmaliges Anmelden (Single Sign-On, SSO) verwendet, aber OIDC verwendet REST/JSON anstelle von XML. OIDC wurde so konzipiert, dass es sowohl mit nativen als auch mit mobilen Apps unter Verwendung von REST/JSON-Protokollen funktioniert. Der primäre Anwendungsfall für SAML sind jedoch webbasierte Apps. Weitere Informationen finden Sie unter [OpenID Connect-Protokoll](https://learn.microsoft.com/de-de/entra/fundamentals/../identity-platform/v2-protocols).

#### JSON-Webtoken (JWTs)

JWTs sind ein offener Standard, der eine kompakte und eigenständige Methode für die sichere Übertragung von Informationen zwischen Parteien als JSON-Objekt definiert. JWTs können überprüft werden und sind vertrauenswürdig, da sie digital signiert sind. Sie können verwendet werden, um die Identität authentifizierter Benutzer zwischen dem Identitätsanbieter und dem Dienst zu übergeben, der die Authentifizierung anfordert. Sie können auch authentifiziert und verschlüsselt werden. Weitere Informationen finden Sie unter [JSON-Webtoken](https://learn.microsoft.com/de-de/entra/fundamentals/../identity-platform/v2-protocols#tokens).

#### Security Assertion Markup Language (SAML)

SAML ist ein offener Standard, der für den Austausch von Authentifizierungs- und Autorisierungsinformationen verwendet wird, in diesem Fall zwischen einer IAM-Lösung und einer anderen Anwendung. Diese Methode nutzt XML zur Übertragung von Daten und wird in der Regel von Identitäts- und Zugriffsverwaltungsplattformen angewendet, um Benutzern die Möglichkeit zu geben, sich bei Anwendungen anzumelden, die in IAM-Lösungen integriert wurden. Weitere Informationen finden Sie unter [SAML-Protokoll](https://learn.microsoft.com/de-de/entra/fundamentals/../identity-platform/saml-protocol-reference).

#### System for Cross-Domain Identity Management (System für domänenübergreifende Identitätsverwaltung, SCIM).

Die SCIM-Bereitstellung wurde entwickelt, um die Verwaltung von Benutzeridentitäten zu vereinfachen. Sie ermöglicht es Organisationen, effizient in der Cloud zu arbeiten und problemlos Benutzer hinzuzufügen oder zu entfernen, wodurch Budgets entlastet, Risiken reduziert und Workflows optimiert werden. SCIM erleichtert auch die Kommunikation zwischen cloudbasierten Anwendungen. Weitere Informationen finden Sie unter [Entwickeln eines SCIM-Endpunkts und Planen seiner Bereitstellung](https://learn.microsoft.com/de-de/entra/fundamentals/../identity/app-provisioning/use-scim-to-provision-users-and-groups?toc=/active-directory/develop/toc.json&bc=/active-directory/develop/breadcrumb/toc.json).

#### Webdiensteverbund (WS-Fed)

WS-Fed wurde von Microsoft entwickelt und wird in den eigenen Anwendungen intensiv genutzt. Dieser Standard definiert die Art und Weise, wie Sicherheitstoken zwischen verschiedenen Einheiten transportiert werden können, um Identitäts- und Autorisierungsinformationen auszutauschen. Weitere Informationen finden Sie unter „Webdiensteverbund-Protokoll“.

## Nächste Schritte

Weitere Informationen finden Sie unter:

* [Einmaliges Anmelden (Single Sign-On, SSO)](https://learn.microsoft.com/de-de/entra/fundamentals/../identity/enterprise-apps/what-is-single-sign-on)
* [Multi-Faktor-Authentifizierung (MFA)](https://learn.microsoft.com/de-de/entra/fundamentals/../identity/authentication/concept-mfa-howitworks)
* [Vergleich: Authentifizierung und Autorisierung](https://learn.microsoft.com/de-de/entra/fundamentals/../identity-platform/authentication-vs-authorization)
* [OAuth 2.0 und OpenID Connect](https://learn.microsoft.com/de-de/entra/fundamentals/../identity-platform/v2-protocols)
* [App-Typen und Authentifizierungsflows](https://learn.microsoft.com/de-de/entra/fundamentals/../identity-platform/authentication-flows-app-scenarios)
* [Sicherheitstoken](https://learn.microsoft.com/de-de/entra/fundamentals/../identity-platform/security-tokens)
