[![Build Status](https://travis-ci.org/fayland/perl-Dancer-Logger-File-PerRequest.svg?branch=master)](https://travis-ci.org/fayland/perl-Dancer-Logger-File-PerRequest)
[![Coverage Status](https://coveralls.io/repos/fayland/perl-Dancer-Logger-File-PerRequest/badge.png?branch=master)](https://coveralls.io/r/fayland/perl-Dancer-Logger-File-PerRequest?branch=master)

# NAME

Dancer::Logger::File::PerRequest - per-request file-based logging engine for Dancer

# SYNOPSIS

    ## in yml config
    logger: "File::PerRequest"

# DESCRIPTION

Dancer::Logger::File::PerRequest is a per-request file-based logging engine for Dancer.

## SETTINGS

### logfile\_callback

By default, it will be generating YYYYMMDDHHMMSS-$pid-$request\_id.log under logs of application dir.

the stuff can be configured as

- per pid

        set 'logfile_callback' => sub {
            return $$ . '.log';
        };

    will genereate $pid.log

- per hour

        set 'logfile_callback' => sub {
            my @d = localtime();
            my $file = sprintf('%04d%02d%02d%02d', $d[5] + 1900, $d[4] + 1, $d[3], $d[2]);
            return $file . '.log';
        };

    will do file as YYYYMMDDHH.log

it's quite flexible that you can configure it as daily or daily + pid + server or whatever.

### log\_path

the log path, same as [Dancer::Logger::File](https://metacpan.org/pod/Dancer::Logger::File), default to $appdir/logs

## HOOKS

### before\_file\_per\_request\_close

    hook 'before_file_per_request_close' => sub {
        my ($fh, $logfile) = @_;

        print $fh "# END on " . scalar(localtime()) . "\n";
    };

### after\_file\_per\_request\_close

    hook 'after_file_per_request_close' => sub {
        my ($logfile, $response) = @_;

        # response as Dancer::Response
        if ($response->status >= 500) { ## server error
            # move file to error dir
        } else {
            # just rm it?
            unlink($logfile);
        }
    };

# AUTHOR

Fayland Lam <fayland@gmail.com>

# COPYRIGHT

Copyright 2014- Fayland Lam

# LICENSE

This library is free software; you can redistribute it and/or modify
it under the same terms as Perl itself.

# SEE ALSO
