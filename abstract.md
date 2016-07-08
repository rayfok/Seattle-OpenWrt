# Porting and Packaging Seattle Testbed for OpenWrt 

Seattle Testbed is an open-source, peer-to-peer platform designed to enhance networking and distributed systems research. Established on the resource donation of users and institutions worldwide, Seattle’s global distribution makes it ideal for applications in cloud computing, networking, and ubiquitous computing. Users can install Seattle for code experimentation in a safe and contained sandboxed environment, limiting the consumption of resources such as CPU, memory, storage space, and network bandwidth, and ensuring that the tested programs are isolated from other files or programs. These characteristics allow distributed code testing without compromising the machine’s performance or security. 

Our goal is to deploy Seattle on OpenWrt, an extensible Linux distribution for embedded devices designed to offer users a full customization of their wireless routers. It includes a fully writable filesystem with a package management system to free the user from the restrictions of vendor application selection and configuration, and a cross-compilation toolchain to build executables across architectures. Compared to computer and mobile environments, modern routers offer limited computational resources and require an optimization of the minimal flash storage available. Solutions include customizing a lightweight image with OpenWrt’s Image Generator or mounting an external filesystem on a USB. A previously explored deployment option for Seattle involves building an IPK, a compressed archive file that could then be installed via OpenWrt's package management system. This approach benefits from the built-in dependency and install location tracking. This project investigates a different deployment approach, using Seattle's Custom Installer Builder to package Seattle's base installers into a tarball for shipment alongside other platforms, with its dependencies managed separately.




 

