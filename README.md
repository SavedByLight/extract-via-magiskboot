MagiskBoot - Boot Image Modification Tool

Usage: ./magiskboot <action> [args...]

Supported actions:
  unpack [-n] [-h] [--vendor] <bootimg>
    Unpack <bootimg> to its individual components, each component to
    a file with its corresponding file name in the current directory.
    Supported components: kernel, kernel_dtb, ramdisk.cpio, second,
    dtb, extra, and recovery_dtbo.
    By default, each component will be decompressed on-the-fly.
    If '-n' is provided, all decompression operations will be skipped;
    each component will remain untouched, dumped in its original format.
    If '-h' is provided, the boot image header information will be
    dumped to the file 'header', which can be used to modify header
    configurations during repacking.
    Return values:
    0:valid    1:error    2:chromeos

  repack [-n] <origbootimg> [outbootimg]
    Repack boot image components using files from the current directory
    to [outbootimg], or 'new-boot.img' if not specified.
    <origbootimg> is the original boot image used to unpack the components.
    By default, each component will be automatically compressed using its
    corresponding format detected in <origbootimg>. If a component file
    in the current directory is already compressed, then no addition
    compression will be performed for that specific component.
    If '-n' is provided, all compression operations will be skipped.
    If env variable PATCHVBMETAFLAG is set to true, all disable flags in
    the boot image's vbmeta header will be set.

  verify <bootimg> [x509.pem]
    Check whether the boot image is signed with AVB 1.0 signature.
    Optionally provide a certificate to verify whether the image is
    signed by the public key certificate.
    Return value:
    0:valid    1:error

  sign <bootimg> [name] [x509.pem pk8]
    Sign <bootimg> with AVB 1.0 signature.
    Optionally provide the name of the image (default: '/boot').
    Optionally provide the certificate/private key pair for signing.
    If the certificate/private key pair is not provided, the AOSP
    verity key bundled in the executable will be used.

  extract <payload.bin> [partition] [outfile]
    Extract [partition] from <payload.bin> to [outfile].
    If [outfile] is not specified, then output to '[partition].img'.
    If [partition] is not specified, then attempt to extract either
    'init_boot' or 'boot'. Which partition was chosen can be determined
    by whichever 'init_boot.img' or 'boot.img' exists.
    <payload.bin> can be '-' to be STDIN.

  hexpatch <file> <hexpattern1> <hexpattern2>
    Search <hexpattern1> in <file>, and replace it with <hexpattern2>

  cpio <incpio> [commands...]
    Do cpio commands to <incpio> (modifications are done in-place).
    Each command is a single argument; add quotes for each command.
    See "cpio --help" for supported commands.

  ramdisk-table <file> [commands...]
    Do ramdisk-table commands to <file> (modifications are done in-place)
    Each command is a single argument, add quotes for each command.
    Supported commands:
      print
        Prints the table
      rm --name <name>
        Removes table entry with name <name>, if present
      add --name <name> --type <type> [--id <0..15> <hex_val>] [--copy-id <entry>]
        Add new table entry with name <name> and type of <type>.
        A ramdisk file with name vendor_ramdisk_<name>.cpio
        has to be present in the current working directory.
        <name> has to be shorter than 32 characters.
        Possible values for <type> are 'none', 'dlkm', 'platform', 'recovery'.
        By default the entire board id is set to zero.
        If --id <index> <hex_val> is specified, the board id
        at index <index> is set to 32 bit <hex_val>.
        It can be specified up to 16 times with different indices.
        If --copy-id <entry> is specified, the entire board id of
        table entry with index <entry> is copied.

  dtb <file> <action> [args...]
    Do dtb related actions to <file>.
    See "dtb --help" for supported actions.

  split <file>
    Split image.*-dtb into kernel + kernel_dtb

  sha1 <file>
    Print the SHA1 checksum for <file>

  cleanup
    Cleanup the current working directory

  compress[=format] <infile> [outfile]
    Compress <infile> with [format] to [outfile].
    <infile>/[outfile] can be '-' to be STDIN/STDOUT.
    If [format] is not specified, then gzip will be used.
    If [outfile] is not specified, then <infile> will be replaced
    with another file suffixed with a matching file extension.
    Supported formats: gzip zopfli xz lzma bzip2 lz4 lz4_legacy lz4_lg 

  decompress <infile> [outfile]
    Detect format and decompress <infile> to [outfile].
    <infile>/[outfile] can be '-' to be STDIN/STDOUT.
    If [outfile] is not specified, then <infile> will be replaced
    with another file removing its archive format file extension.
    Supported formats: gzip zopfli xz lzma bzip2 lz4 lz4_legacy lz4_lg 