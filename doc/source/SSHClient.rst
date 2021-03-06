.. SSHClient and SSHAuth

API: SSHClient and SSHAuth.
===========================

.. py:module:: exec_helpers
.. py:currentmodule:: exec_helpers

.. py:class:: SSHClient

    SSHClient helper.

    .. py:method:: __init__(host, port=22, username=None, password=None, private_keys=None, auth=None, )

        :param host: remote hostname
        :type host: ``str``
        :param port: remote ssh port
        :type port: ``int``
        :param username: remote username.
        :type username: ``typing.Optional[str]``
        :param password: remote password
        :type password: ``typing.Optional[str]``
        :param private_keys: private keys for connection
        :type private_keys: ``typing.Optional[typing.Iterable[paramiko.RSAKey]]``
        :param auth: credentials for connection
        :type auth: typing.Optional[SSHAuth]
        :param verbose: show additional error/warning messages
        :type verbose: bool

    .. note:: auth has priority over username/password/private_keys

    .. py:attribute:: log_mask_re

        ``typing.Optional[str]``

        regex lookup rule to mask command for logger. all MATCHED groups will be replaced by '<*masked*>'

    .. py:attribute:: lock

        ``threading.RLock``
        Connection lock for protection from destructive race-conditions (close/reconnect/...)

    .. py:attribute:: logger

        ``logging.Logger``
        Internal logger.

    .. py:attribute:: auth

        Internal authorisation object

        :rtype: SSHAuth

    .. py:attribute:: hostname

        ``str``
        Connection hostname

    .. py:attribute:: port

        ``int``
        Connection port

    .. py:attribute:: is_alive

        ``bool``
        Paramiko status: ready to use|reconnect required

    .. py:attribute:: sudo_mode

        ``bool``
        Use sudo for all calls, except wrapped in connection.sudo context manager.

    .. py:attribute:: keepalive_mode

        ``bool``
        Use keepalive mode for context manager. If `False` - close connection on exit from context manager.

    .. py:method:: close()

        Close connection

    .. py:classmethod:: close()

        Close all memorized connections

    .. py:method:: reconnect()

        Reconnect SSH session

    .. py:method:: __enter__()

        Open context manager

        .. versionchanged:: 1.1.0 lock on enter

    .. py:method:: __exit__(self, exc_type, exc_val, exc_tb)

        Close context manager and disconnect

        .. versionchanged:: 1.0.0 disconnect enforced on close
        .. versionchanged:: 1.1.0 release lock on exit
        .. versionchanged:: 1.2.1 disconnect enforced on close only not in keepalive mode

    .. py:method:: sudo(enforce=None)

        Context manager getter for sudo operation

        :param enforce: Enforce sudo enabled or disabled. By default: None
        :type enforce: ``typing.Optional[bool]``
        :rtype: ``typing.ContextManager``

    .. py:method:: keepalive(enforce=None)

        Context manager getter for keepalive operation.

        :param enforce: Enforce keepalive enabled or disabled. By default: True
        :type enforce: ``typing.bool``
        :rtype: ``typing.ContextManager``

        .. Note:: Enter and exit ssh context manager is produced as well.
        .. versionadded:: 1.2.1

    .. py:method:: execute_async(command, stdin=None, open_stdout=True, open_stderr=True, verbose=False, log_mask_re=None, **kwargs)

        Execute command in async mode and return channel with IO objects.

        :param command: Command for execution
        :type command: ``str``
        :param stdin: pass STDIN text to the process
        :type stdin: ``typing.Union[str, bytes, bytearray, None]``
        :param open_stdout: open STDOUT stream for read
        :type open_stdout: bool
        :param open_stderr: open STDERR stream for read
        :type open_stderr: bool
        :param verbose: produce verbose log record on command call
        :type verbose: bool
        :param log_mask_re: regex lookup rule to mask command for logger.
                            all MATCHED groups will be replaced by '<*masked*>'
        :type log_mask_re: typing.Optional[str]
        :rtype: ``typing.Tuple[paramiko.Channel, paramiko.ChannelFile, typing.Optional[paramiko.ChannelFile], typing.Optional[paramiko.ChannelFile]]``

        .. versionchanged:: 1.2.0 open_stdout and open_stderr flags
        .. versionchanged:: 1.2.0 stdin data
        .. versionchanged:: 1.2.0 get_pty moved to `**kwargs`

    .. py:method:: execute(command, verbose=False, timeout=1*60*60, **kwargs)

        Execute command and wait for return code.

        :param command: Command for execution
        :type command: ``str``
        :param verbose: Produce log.info records for command call and output
        :type verbose: ``bool``
        :param timeout: Timeout for command execution.
        :type timeout: ``typing.Union[int, float, None]``
        :rtype: ExecResult
        :raises ExecHelperTimeoutError: Timeout exceeded

        .. versionchanged:: 1.2.0 default timeout 1 hour

    .. py:method:: check_call(command, verbose=False, timeout=1*60*60, error_info=None, expected=None, raise_on_err=True, **kwargs)

        Execute command and check for return code.

        :param command: Command for execution
        :type command: ``str``
        :param verbose: Produce log.info records for command call and output
        :type verbose: ``bool``
        :param timeout: Timeout for command execution.
        :type timeout: ``typing.Union[int, float, None]``
        :param error_info: Text for error details, if fail happens
        :type error_info: ``typing.Optional[str]``
        :param expected: expected return codes (0 by default)
        :type expected: ``typing.Optional[typing.Iterable[int]]``
        :param raise_on_err: Raise exception on unexpected return code
        :type raise_on_err: ``bool``
        :rtype: ExecResult
        :raises ExecHelperTimeoutError: Timeout exceeded
        :raises CalledProcessError: Unexpected exit code

        .. versionchanged:: 1.2.0 default timeout 1 hour

    .. py:method:: check_stderr(command, verbose=False, timeout=1*60*60, error_info=None, raise_on_err=True, **kwargs)

        Execute command expecting return code 0 and empty STDERR.

        :param command: Command for execution
        :type command: ``str``
        :param verbose: Produce log.info records for command call and output
        :type verbose: ``bool``
        :param timeout: Timeout for command execution.
        :type timeout: ``typing.Union[int, float, None]``
        :param error_info: Text for error details, if fail happens
        :type error_info: ``typing.Optional[str]``
        :param raise_on_err: Raise exception on unexpected return code
        :type raise_on_err: ``bool``
        :rtype: ExecResult
        :raises ExecHelperTimeoutError: Timeout exceeded
        :raises CalledProcessError: Unexpected exit code or stderr presents

        .. note:: expected return codes can be overridden via kwargs.
        .. versionchanged:: 1.2.0 default timeout 1 hour

    .. py:method:: execute_through_host(hostname, command, auth=None, target_port=22, verbose=False, timeout=1*60*60, get_pty=False, **kwargs)

        Execute command on remote host through currently connected host.

        :param hostname: target hostname
        :type hostname: ``str``
        :param command: Command for execution
        :type command: ``str``
        :param auth: credentials for target machine
        :type auth: typing.Optional[SSHAuth]
        :param target_port: target port
        :type target_port: ``int``
        :param verbose: Produce log.info records for command call and output
        :type verbose: ``bool``
        :param timeout: Timeout for command execution.
        :type timeout: ``typing.Union[int, float, None]``
        :param get_pty: open PTY on target machine
        :type get_pty: ``bool``
        :rtype: ExecResult
        :raises ExecHelperTimeoutError: Timeout exceeded

        .. versionchanged:: 1.2.0 default timeout 1 hour

    .. py:classmethod:: execute_together(remotes, command, timeout=1*60*60, expected=None, raise_on_err=True, **kwargs)

        Execute command on multiple remotes in async mode.

        :param remotes: Connections to execute on
        :type remotes: typing.Iterable[SSHClient]
        :param command: Command for execution
        :type command: ``str``
        :param timeout: Timeout for command execution.
        :type timeout: ``typing.Union[int, float, None]``
        :param expected: expected return codes (0 by default)
        :type expected: ``typing.Optional[typing.Iterable[]]``
        :param raise_on_err: Raise exception on unexpected return code
        :type raise_on_err: ``bool``
        :return: dictionary {(hostname, port): result}
        :rtype: typing.Dict[typing.Tuple[str, int], ExecResult]
        :raises ParallelCallProcessError: Unexpected any code at lest on one target
        :raises ParallelCallExceptions: At lest one exception raised during execution (including timeout)

        .. versionchanged:: 1.2.0 default timeout 1 hour

    .. py:method:: open(path, mode='r')

        Open file on remote using SFTP session.

        :type path: ``str``
        :type mode: ``str``

    .. py:method:: exists(path)

        Check for file existence using SFTP session.

        :type path: ``str``
        :rtype: ``bool``

    .. py:method:: stat(path)

        Get stat info for path with following symlinks.

        :type path: ``str``
        :rtype: ``paramiko.sftp_attr.SFTPAttributes``

    .. py:method:: utime(path, times=None):

        Set atime, mtime.

        :param path: filesystem object path
        :type path: str
        :param times: (atime, mtime)
        :type times: typing.Optional[typing.Tuple[int, int]]
        :rtype: None

        .. versionadded:: 1.0.0

    .. py:method:: isfile(path)

        Check, that path is file using SFTP session.

        :type path: ``str``
        :rtype: ``bool``

    .. py:method:: isdir(path)

        Check, that path is directory using SFTP session.

        :type path: ``str``
        :rtype: ``bool``

    **Non standard methods:**

    .. py:method:: mkdir(path)

        run 'mkdir -p path' on remote.

        :type path: ``str``

    .. py:method:: rm_rf(path)

        run 'rm -rf path' on remote.

        :type path: ``str``

    .. py:method:: upload(source, target)

        Upload file(s) from source to target using SFTP session.

        :type source: ``str``
        :type target: ``str``

    .. py:method:: download(destination, target)

        Download file(s) to target from destination.

        :type destination: ``str``
        :type target: ``str``
        :return: downloaded file present on local filesystem
        :rtype: ``bool``


.. py:class:: SSHAuth(object)

    SSH credentials object.

    Used to authorize SSHClient.
    Single SSHAuth object is associated with single host:port.
    Password and key is private, other data is read-only.

    .. py:method:: __init__(username=None, password=None, key=None, keys=None, )

        :param username: remote username.
        :type username: ``typing.Optional[str]``
        :param password: remote password
        :type password: ``typing.Optional[str]``
        :param key: Main connection key
        :type key: ``typing.Optional[paramiko.RSAKey]``
        :param keys: Alternate connection keys
        :type keys: ``typing.Optional[typing.Iterable[paramiko.RSAKey]]``
        :param key_filename: filename(s) for additional key files
        :type key_filename: ``typing.Union[typing.List[str], str, None]``
        :param passphrase: passphrase for keys. Need, if differs from password
        :type passphrase: ``typing.Optional[str]``

        .. versionchanged:: 1.0.0
            added: key_filename, passphrase arguments

    .. py:attribute:: username

        ``typing.Optional[str]``

    .. py:attribute:: public_key

        ``typing.Optional[str]``
        public key for stored private key if presents else None

    .. py:attribute:: key_filename

        ``typing.Union[typing.List[str], str, None]``
        Key filename(s).

        .. versionadded:: 1.0.0

    .. py:method:: enter_password(self, tgt)

        Enter password to STDIN.

        Note: required for 'sudo' call

        :param tgt: Target
        :type tgt: file

    .. py:method:: connect(client, hostname=None, port=22, log=True, )

        Connect SSH client object using credentials.

        :param client: SSH Client (low level)
        :type client: ``typing.Union[paramiko.client.SSHClient, paramiko.transport.Transport]``
        :param hostname: remote hostname
        :type hostname: ``str``
        :param port: remote ssh port
        :type port: ``int``
        :param log: Log on generic connection failure
        :type log: ``bool``
        :raises paramiko.AuthenticationException: Authentication failed.
