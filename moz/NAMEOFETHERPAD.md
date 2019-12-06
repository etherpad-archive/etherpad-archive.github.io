<!--
             _.-~-.
           7''  Q..\
        _7         (_
      _7  _/    _q.  /
    _7 . ___  /VVvv-'_                                            .
   7/ / /~- \_\\      '-._     .-'                      /       //
  ./ ( /-~-/||'=.__  '::. '-~'' {             ___   /  //     ./{
 V   V-~-~| ||   __''_   ':::.   ''~-~.___.-'' _/  // / {_   /  {  /
  VV/-~-~-|/ \ .'__'. '.    '::                     _ _ _        ''.
  / /~~~~||VVV/ /  \ )  \        _ __ ___   ___ ___(_) | | __ _   .::'
 / (~-~-~\\.-' /    \'   \::::. | '_ ` _ \ / _ \_  / | | |/ _` | :::'
/..\    /..\__/      '     '::: | | | | | | (_) / /| | | | (_| | ::'
vVVv    vVVv                 ': |_| |_| |_|\___/___|_|_|_|\__,_| ''

This is the custom Mozilla Login Experience, designed and built by Mozilla's IAM Project.

Version: 1.6.0
Changelog: https://github.com/mozilla-iam/auth0-custom-lock/blob/master/CHANGELOG.md

These are the variables we are using:

  Auth0-domain: auth.mozilla.auth0.com
  Client ID: 
  LDAP connection name: Mozilla-LDAP
  Person API domain: https://person-api.sso.mozilla.com/v1/connection/
  Google Tag Manager ID: GTM-T2N2BRW
  Logout URL: https://auth.mozilla.auth0.com/v2/logout?returnTo=https://sso.mozilla.com/signout.html
  SSO Dashboard URL: https://sso.mozilla.com
  CSP: default-src 'none'; connect-src 'self' https://person-api.sso.mozilla.com; script-src 'self' https://cdn.sso.mozilla.com https://www.googletagmanager.com https://www.google-analytics.com; style-src 'self' https://cdn.sso.mozilla.com; font-src 'self' https://cdn.sso.mozilla.com; img-src 'self' https://cdn.sso.mozilla.com https://www.google-analytics.com
  CDN: https://cdn.sso.mozilla.com/nlx/b2ac7a8
  Features:
   autologin: true
   person_api_lookup: true
   supportedLoginMethods: github,google-oauth2,firefoxaccounts,email
  Display names:
    github: GitHub
    google-oauth2: Google
    firefoxaccounts: Firefox Accounts
    email: email

The code is the result of a build process, please find the source code
on GitHub: https://github.com/mozilla-iam/auth0-custom-lock
-->
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Mozilla Login</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta http-equiv="Content-Security-Policy" content="default-src 'none'; connect-src 'self' https://person-api.sso.mozilla.com; script-src 'self' https://cdn.sso.mozilla.com https://www.googletagmanager.com https://www.google-analytics.com; style-src 'self' https://cdn.sso.mozilla.com; font-src 'self' https://cdn.sso.mozilla.com; img-src 'self' https://cdn.sso.mozilla.com https://www.google-analytics.com">
    <link rel="dns-prefetch" href="https://person-api.sso.mozilla.com/v1/connection/">
    <link rel="stylesheet" href="https://cdn.sso.mozilla.com/nlx/b2ac7a8/css/fonts.css" type="text/css" />
    <link rel="stylesheet" href="https://cdn.sso.mozilla.com/nlx/b2ac7a8/css/styles.css" type="text/css" />
    <link rel="icon" href="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/favicon.png" type="image/png" sizes="192x192" />
  </head>
  <body data-decorator="load-ga display-rp-name prevent-clickjack track-password-manager-usage check-keyboard">
    <div data-decorator="check-if-maintenance-mode" hidden>
      <div class="banner">
        <p>We are currently experiencing an issue &amp; working hard to resolve it. Please try to login again in a few minutes.  Request help via <a href="mailto:moc@mozilla.com">moc@mozilla.com</a>, IRC #moc or Slack #mozsm-all.</p>
      </div>
    </div>
    <div class="card">
      <a href="https://mozilla.org" class="logo" target="_blank"><img alt="Mozilla" src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/mozilla.svg" /></a>
      <noscript><p>This login form requires JavaScript to work, please enable it to log in.</p></noscript>
      <form id="nlx-form" data-decorator="init-auth handle-submit" method="post" action="/" lock-state="initial">
        <fieldset class="hidden-lastpass-password-offset" data-decorator="decide-screen">
          <legend class="rp-name"><span class="visually-hidden">Log in to </span><span id="rp-name-placeholder" class="rp-name__text"></span></legend>
          <div id="loading" class="loading" data-screen hidden>
            <p class="visually-hidden">Loading…</p>
            <img src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/loading.svg" class="loading__spinner" alt="" />
            <p role="alert" id="loading__status" class="loading__status"></p>
          </div>
          <div id="initial" data-screen hidden>
            <div class="form__input">
              <input type="email" name="username" id="field-email" autocomplete="email" autofocus data-decorator="watch-contents" />
              <label for="field-email"><span><span aria-hidden="true">Log in with </span>email</span></label>
            </div>
            <input type="submit" class="button button--full-width" id="enter-initial" value="Enter" />
            <hr>
            <ul class="login-options list list--plain">
              <li data-optional-login-method="firefoxaccounts">
                <button class="button button--full-width button--social button--secondary" type="button" data-handler="authorise-firefoxaccounts">
                  <img class="icon" src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/firefox.svg" alt="">
                  <span>Log in with Firefox</span>
                </button>
              </li>
              <li data-optional-login-method="github">
                <button class="button button--github button--full-width button--social button--secondary" type="button" data-handler="authorise-github">
                  <img class="icon icon-default" src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/github.svg" alt="">
                  <img class="icon icon-hover" src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/github-white.svg" alt="">
                  <span>Log in with GitHub</span>
                </button>
              </li>
              <li data-optional-login-method="google-oauth2">
                <button class="button button--full-width button--social button--secondary" type="button" data-handler="authorise-google">
                  <img class="icon" src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/google.svg" alt="">
                  <span>Log in with Google</span>
                </button>
            </ul>
            <div data-decorator="hide-autologin-setting-conditionally">
              <hr>
              <div class="setting">
                <a href="#what-is-autologin" class="link-to-tooltip" data-decorator="tooltip"><span class="visually-hidden">What is Auto-login?</span> <img class="icon" src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/info.svg" alt=""></a>
                <input type="checkbox" id="use-autologin" data-decorator="set-autologin-preference" checked>
                <label for="use-autologin"><span class="setting__toggle"></span> <span class="setting__enabled-text">Auto-login is enabled</span><span class="setting__disabled-text">Auto-login is disabled</span></label>
                <div id="what-is-autologin" class="tooltip">
                  <p>Auto-login will allow you to bypass this screen and be automatically logged in via your chosen login method. This setting can be edited by visiting your menu on the SSO Dashboard.</p>
                </div>
              </div>
            </div>
          </div>
          <div id="initial-login-signup" data-screen hidden>
            <div class="form__input">
              <input type="email" name="username" id="field-email-signup" autocomplete="email" data-decorator="watch-contents" />
              <label for="field-email-signup"><span><span aria-hidden="true">Log In or Sign Up with </span>email</span></label>
            </div>
            <input type="submit" class="button button--full-width" id="enter-initial-signup" value="Enter" />
            <hr>
            <ul class="login-options list list--plain">
              <li data-optional-login-method="firefoxaccounts">
                <button class="button button--full-width button--social button--secondary" type="button" data-handler="authorise-firefoxaccounts">
                  <img class="icon" src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/firefox.svg" alt="">
                  <span>Continue with Firefox</span>
                </button>
              </li>
              <li data-optional-login-method="github">
                <button class="button button--github button--full-width button--social button--secondary" type="button" data-handler="authorise-github">
                  <img class="icon icon-default" src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/github.svg" alt="">
                  <img class="icon icon-hover" src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/github-white.svg" alt="">
                  <span>Continue with GitHub</span>
                </button>
              </li>
              <li data-optional-login-method="google-oauth2">
                <button class="button button--full-width button--social button--secondary" type="button" data-handler="authorise-google">
                  <img class="icon" src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/google.svg" alt="">
                  <span>Continue with Google</span>
                </button>
            </ul>
            <div data-decorator="hide-autologin-setting-conditionally">
              <hr>
              <div class="setting">
                <a href="#what-is-autologin-2" class="link-to-tooltip" data-decorator="tooltip"><span class="visually-hidden">What is Auto-login?</span> <img class="icon" src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/info.svg" alt=""></a>
                <input type="checkbox" id="use-autologin-2" data-decorator="set-autologin-preference" checked>
                <label for="use-autologin-2"><span class="setting__toggle"></span> <span class="setting__enabled-text">Auto-login is enabled</span><span class="setting__disabled-text">Auto-login is disabled</span></label>
                <div id="what-is-autologin-2" class="tooltip">
                  <p>Auto-login will allow you to bypass this screen and be automatically logged in via your chosen login method. This setting can be edited by visiting your menu on the SSO Dashboard.</p>
                </div>
              </div>
            </div>
          </div>
          <div id="ldap" data-screen hidden>
            <div class="form__input">
              <input type="password" id="field-password" autocomplete="current-password" name="password" data-decorator="submit-with-enter watch-contents" data-continue-with="authorise-ldap-credentials"  />
              <label for="field-password"><span>LDAP password</span></label>
            </div>
            <input type="submit" class="button button--full-width" id="authorise-ldap-credentials" value="Enter"/>
            <hr>
            <button type="button" class="button button--text-only card__back button--unpadded" data-handler="go-to-initial-page">Need to start over with a different account?</button>
          </div>
          <div id="non-ldap" data-screen hidden>
            <ul class="login-options list list--plain" data-optional-login-method="email">
              <li>
                <button class="button button--full-width button--secondary" type="button" data-handler="send-passwordless-link">Send me an email to continue</button>
              </li>
            </ul>
            <a href="https://wiki.mozilla.org/IAM/Frequently_asked_questions#Q:_Why_do_you_support_email_login_.28.22passwordless.22.29_if_it.27s_less_safe_than_other_methods.3F" class="button button--text-only button--unpadded" target="_blank">Why an email?</a>
            <hr>
            <ul class="login-options list list--plain">
              <li data-optional-login-method="firefoxaccounts">
                <button class="button button--full-width button--social button--secondary" type="button" data-handler="authorise-firefoxaccounts">
                  <img class="icon" src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/firefox.svg" alt="">
                  <span>Continue with Firefox</span>
                </button>
              </li>
              <li data-optional-login-method="github">
                <button class="button button--github button--full-width button--social button--secondary" type="button" data-handler="authorise-github">
                  <img class="icon icon-default" src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/github.svg" alt="">
                  <img class="icon icon-hover" src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/github-white.svg" alt="">
                  <span>Continue with GitHub</span>
                </button>
              </li>
              <li data-optional-login-method="google-oauth2">
                <button class="button button--full-width button--social button--secondary" type="button" data-handler="authorise-google">
                  <img class="icon" src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/google.svg" alt="">
                  <span>Continue with Google</span>
                </button>
            </ul>
            <hr>
            <button type="button" class="button button--text-only card__back button--unpadded" data-handler="go-to-initial-page">Need to start over with a different account?</button>
          </div>
          <div id="passwordless-success" data-screen hidden>
            <h2 class="card__heading card__heading--success"><img src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/envelope.svg" class="card__heading-icon" alt="" /> <span>Success</span></h2>
            <p>Email sent to <span id="passwordless-success-email-address"></span>, go to your inbox to continue. Your login link will expire in 15 minutes.</p>
            <hr>
            <button id="back" type="button" class="button button--text-only card__back  button--unpadded" data-handler="go-to-initial-page">Need to send that to a different email?</button>
          </div>
          <div id="error-password" data-screen hidden>
            <h2 class="card__heading card__heading--error" id="error-message-ldap"><img src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/circled-x.svg" class="card__heading-icon" alt="" /> <span>Error</span></h2>
            <p>Please attempt again or contact the Help Desk for assistance.</p>
            <div class="form__input">
              <input type="password" id="field-password-try-2" autocomplete="current-password" name="password" data-decorator="submit-with-enter watch-contents" data-continue-with="authorise-ldap-credentials"  />
              <label for="field-password-try-2"><span>LDAP password</span></label>
            </div>
            <input type="submit" class="button button--full-width" id="authorise-ldap-credentials-try-2" value="Enter" />
            <hr>
            <button type="button" class="button button--text-only card__back button--unpadded" data-handler="go-to-initial-page">Need to start over with a different account?</button>
          </div>
          <div id="error-passwordless" data-screen hidden>
            <h2 class="card__heading card__heading--error" id="error-message-passwordless"><img src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/circled-x.svg" class="card__heading-icon" alt="" /> <span>Error</span></h2>
            <p>We cannot send an email to this address</p>
            <button type="button" class="button button--text-only card__back  button--unpadded" data-handler="go-to-initial-page">Try again</button>
            <hr>
            <button type="button" class="button button--text-only card__back button--unpadded" data-handler="go-to-initial-page">Need to start over with a different account?</button>
          </div>
          <div id="ldap-not-available" data-screen hidden>
            <h2 class="card__heading card__heading--error"><img src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/circled-x.svg" class="card__heading-icon" alt="" /> <span>Error</span></h2>
            <p>Unfortunately, this website does not offer login with Mozilla LDAP.</p>
            <hr>
            <button type="button" class="button button--text-only card__back button--unpadded" data-handler="go-to-initial-page">Need to start over with a different account?</button>
          </div>
          <div id="ldap-required" data-screen hidden>
            <h2 class="card__heading card__heading--error"><img src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/images/circled-x.svg" class="card__heading-icon" alt="/> <span>Error</span></h2>
            <p>Unfortunately, you can only login to this website using Mozilla LDAP.</p>
            <hr>
            <button type="button" class="button button--text-only card__back button--unpadded" data-handler="go-to-initial-page">Need to start over with a different account?</button>
          </div>
          <div id="autologin-settings" data-screen hidden>
            <h2 class="card__heading card__heading--iconless">Auto-login settings</h2>
            <p data-decorator="fill-in-session-info">You are currently logged in<span data-email hidden> as <strong></strong></span><span data-login-method hidden> using <strong></strong></span>.</p>
            <div class="setting">
              <input type="checkbox" id="use-autologin-2" data-decorator="set-autologin-preference" checked>
              <label for="use-autologin-2"><span class="setting__toggle"></span> <span class="setting__enabled-text">Auto-login is enabled</span><span class="setting__disabled-text">Auto-login is disabled</span></label>
              <input type="checkbox" id="use-autologin-3" data-decorator="set-autologin-preference" checked>
              <label for="use-autologin-3"><span class="setting__toggle"></span> <span class="setting__enabled-text">Auto-login is enabled</span><span class="setting__disabled-text">Auto-login is disabled</span></label>
            </div>
            <hr>
            <ul class="login-options list list--plain">
              <li><a href="https://sso.mozilla.com" class="button button--full-width button--secondary">Back to Dashboard</a></li>
              <li><a href="https://sso.mozilla.com/logout" class="button button--full-width button--secondary">Log out of Mozilla</a></li>
            </ul>
          </div>
          <input type="password" id="hidden-lastpass-password" autocomplete="current-password" name="hidden-lastpass-password" aria-hidden />
        </fieldset>
      </form>
      <ul class="legal-links list list--plain">
        <li><a href="https://www.mozilla.org/en-US/about/legal/" target="_blank">Legal</a></li>
        <li><a href="https://www.mozilla.org/en-US/privacy/websites/" target="_blank">Privacy</a></li>
        <li><a href="https://discourse.mozilla.org/c/iam" target="_blank">Need help?</a></li>
      </ul>
    </div>
    <script type="text/json" id="nlx-config">
    {
      "hostedConfig": "eyJpY29uIjoiaHR0cHM6Ly9zc28ubW96aWxsYS5jb20vc3RhdGljL2ltZy9tb3ppbGxhLnN2ZyIsImFzc2V0c1VybCI6Imh0dHBzOi8vcnRhLm1vemlsbGEuYXV0aDAuY29tLyIsImF1dGgwRG9tYWluIjoiYXV0aC5tb3ppbGxhLmF1dGgwLmNvbSIsImF1dGgwVGVuYW50IjoiYXV0aCIsImNhbGxiYWNrT25Mb2NhdGlvbkhhc2giOmZhbHNlLCJjYWxsYmFja1VSTCI6Imh0dHBzOi8vcGFkLm1vemlsbGEub3JnL29wZW5pZC9jYWxsYmFjay9sb2dpbi8iLCJjZG4iOiJodHRwczovL3J0YS5tb3ppbGxhLmF1dGgwLmNvbS8iLCJjbGllbnRJRCI6IlRTWXI2dFpjeWJIeHRPbFl6bkIxZnNsQ3ZDSW5uS3czIiwiZGljdCI6eyJzaWduaW4iOnsidGl0bGUiOiJwYWQubW96aWxsYS5vcmcifX0sImV4dHJhUGFyYW1zIjp7InByb3RvY29sIjoib2F1dGgyIiwicmVzcG9uc2VfdHlwZSI6ImNvZGUiLCJub25jZSI6IjUxOTNhMjcxNmM0ZWE2YTRkMzE5ZjYxYWNmZWViNDMwIiwic2NvcGUiOiJvcGVuaWQgZW1haWwgcHJvZmlsZSIsIl9jc3JmIjoiRGZXMjlIZDgtYzNFRnBBUFNmR3dlNU9YMVBnU1cwcHB6WFV3IiwiX2ludHN0YXRlIjoiZGVwcmVjYXRlZCIsInN0YXRlIjoiZzZGbzJTQXhlbHBGYm1sTk5Xc3RSV0YwVHpsUlRWVmFNVXB5T0V0M2R6WlRNa2hJT0tOMGFXVFpJRzlTYzNCQlp6bEtiV3hKYmtoSVRtTk1RbTlOYXpsWWJHNTFhbE5IU21sS28yTnBaTmtnVkZOWmNqWjBXbU41WWtoNGRFOXNXWHB1UWpGbWMyeERka05KYm01TGR6TSJ9LCJpbnRlcm5hbE9wdGlvbnMiOnsicHJvdG9jb2wiOiJvYXV0aDIiLCJyZXNwb25zZV90eXBlIjoiY29kZSIsIm5vbmNlIjoiNTE5M2EyNzE2YzRlYTZhNGQzMTlmNjFhY2ZlZWI0MzAiLCJzY29wZSI6Im9wZW5pZCBlbWFpbCBwcm9maWxlIiwiX2NzcmYiOiJEZlcyOUhkOC1jM0VGcEFQU2ZHd2U1T1gxUGdTVzBwcHpYVXciLCJfaW50c3RhdGUiOiJkZXByZWNhdGVkIiwic3RhdGUiOiJnNkZvMlNBeGVscEZibWxOTldzdFJXRjBUemxSVFZWYU1VcHlPRXQzZHpaVE1raElPS04wYVdUWklHOVNjM0JCWnpsS2JXeEpia2hJVG1OTVFtOU5hemxZYkc1MWFsTkhTbWxLbzJOcFpOa2dWRk5aY2paMFdtTjVZa2g0ZEU5c1dYcHVRakZtYzJ4RGRrTkpibTVMZHpNIn0sIndpZGdldFVybCI6Imh0dHBzOi8vY2RuLmF1dGgwLmNvbS93Mi9hdXRoMC13aWRnZXQtNS4yLm1pbi5qcyIsImlzVGhpcmRQYXJ0eUNsaWVudCI6ZmFsc2UsImF1dGhvcml6YXRpb25TZXJ2ZXIiOnsidXJsIjoiaHR0cHM6Ly9hdXRoLm1vemlsbGEuYXV0aDAuY29tIiwiaXNzdWVyIjoiaHR0cHM6Ly9hdXRoLm1vemlsbGEuYXV0aDAuY29tLyJ9LCJjb2xvcnMiOnsicHJpbWFyeSI6IiMwMDAwMDAiLCJwYWdlX2JhY2tncm91bmQiOiIjMDAwMDAwIn19",
      "environment": "PROD",
      "domain": "auth.mozilla.auth0.com",
      "client_ID": "",
      "LDAP_connection_name": "Mozilla-LDAP",
      "person_api_domain": "https://person-api.sso.mozilla.com/v1/connection/",
      "GTM_ID": "GTM-T2N2BRW",
      "features" : {
        "autologin": "true",
        "person_api_lookup": "true",
        "maintenance_mode": "false"
      },
      "supportedLoginMethods": "github,google-oauth2,firefoxaccounts,email",
      "logout_url": "https://auth.mozilla.auth0.com/v2/logout?returnTo=https://sso.mozilla.com/signout.html",
      "displayNames": {
        "github": "GitHub",
        "google-oauth2": "Google",
        "firefoxaccounts": "Firefox Accounts",
        "email": "email"
      }
   }</script>
    <script src="https://cdn.sso.mozilla.com/nlx/b2ac7a8/js/main.js"></script>
  </body>
</html>
