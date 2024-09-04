# Nix Shells Video

## commands used:

```bash
# enter a shell with a package
nix-shell -p nodejs

# enter a shell with a package (nix-command)
nix shell nixpkgs#nodejs

# search for packages in the nix store
find / -type d -name bin -exec find {} -type f -name nvim \; 2>/dev/null

# garbage collect nix store
nix-collect-garbage -d

# enter a shell with several packages
nix-shell -p nodejs neovim git

# package building shells
nix-shell -E 'with import <nixpkgs> { }; callPackage ./default.nix { }'
```

## A basic shell.nix file

```nix
{ pkgs ? import <nixpkgs> {} }:

pkgs.mkShell {

  packages = [ pkgs.nodejs pkgs.python3 ];

  inputsFrom = [ pkgs.bat ];

  shellHook = ''
    echo "welcome to the shell!"
  '';

  test = "AAAAAA";
  ENVVAR = "testtt";

  LD_LIBRARY_PATH = 
    "${pkgs.lib.makeLibraryPath [pkgs.ncurses]}";
  
  RUST_BACKTRACE = 1;

}
```

## A flake with a shell

```nix
{
  description = "A very basic flake";

  inputs = {
    nixpkgs.url = "github:nixos/nixpkgs?ref=nixos-unstable";
  };

  outputs = { self, nixpkgs }: 
  let
    pkgs = nixpkgs.legacyPackages."x86_64-linux";
  in
  {
    # importing package example
    # packages."x86_64-linux".default = 
    #   pkgs.callPackage (import ./default.nix) {};

    devShells."x86_64-linux".default = pkgs.mkShell {

      packages = [ pkgs.nodejs pkgs.python3 ];

      inputsFrom = [ pkgs.bat ];
      
    };
  };
}
`
```
