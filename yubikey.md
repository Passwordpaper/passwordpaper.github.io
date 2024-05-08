---
layout: math
---

# To connect to ssh with yubikey

gpg-agent --enable-ssh-support --daemon ssh -vp 39826 user@hostname
        
stop. do you have pgp.key?

kk. continue

To associate gpg-agent with current key use:
* gpg-connect-agent "scd serialno" "learn --force" /bye

## OK that didn't work
* get keygrip with gpg -K --with-keygrip and put it in ~/.gnupg/sshcontrol</li>
* paste enable-ssh-support into ~/.gnupg/gpg-agent.conf if it's not there</li>
* export SSH_AUTH_SOCK=$(gpgconf --list-dirs agent-ssh-socket)</li>
* gpgconf --launch gpg-agent</li>

## Time to test math

\begin{equation}
\label{testin}
\int_0^x \frac{d~cabin}{cabin}
\end{equation}

You can now reference equation $$\eqref{testin}$$.
