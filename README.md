# Summary
Repository related to https://github.com/embassy-rs/embassy/issues/1904.

Apply this diff to https://github.com/embassy-rs/embassy/ master branch (ex. [this commit](https://github.com/embassy-rs/embassy/commit/7c77d2bd94afcc5dc174f9b3e5c2ce947c6fabb0)) to get blinky running on nRF52832.
```diff
diff --git a/examples/nrf52840/.cargo/config.toml b/examples/nrf52840/.cargo/config.toml
index 17616a05..24255195 100644
--- a/examples/nrf52840/.cargo/config.toml
+++ b/examples/nrf52840/.cargo/config.toml
@@ -1,6 +1,6 @@
 [target.'cfg(all(target_arch = "arm", target_os = "none"))']
 # replace nRF82840_xxAA with your chip as listed in `probe-rs chip list`
-runner = "probe-rs run --chip nRF52840_xxAA"
+runner = "probe-rs run --chip nRF52832_xxAA"

 [build]
 target = "thumbv7em-none-eabi"
diff --git a/examples/nrf52840/Cargo.toml b/examples/nrf52840/Cargo.toml
index 8b7121ab..b2762112 100644
--- a/examples/nrf52840/Cargo.toml
+++ b/examples/nrf52840/Cargo.toml
@@ -32,7 +32,7 @@ embassy-futures = { version = "0.1.0", path = "../../embassy-futures" }
 embassy-sync = { version = "0.2.0", path = "../../embassy-sync", features = ["defmt"] }
 embassy-executor = { version = "0.3.0", path = "../../embassy-executor", features = ["arch-cortex-m", "executor-thread", "executor-interrupt", "defmt", "integrated-timers"] }
 embassy-time = { version = "0.1.3", path = "../../embassy-time", features = ["defmt", "defmt-timestamp-uptime"] }
-embassy-nrf = { version = "0.1.0", path = "../../embassy-nrf", features = ["defmt", "nrf52840", "time-driver-rtc1", "gpiote", "unstable-pac", "time"] }
+embassy-nrf = { version = "0.1.0", path = "../../embassy-nrf", features = ["defmt", "nrf52832", "time-driver-rtc1", "gpiote", "unstable-pac", "time"] }
 embassy-net = { version = "0.1.0", path = "../../embassy-net", features = ["defmt", "tcp", "dhcpv4", "medium-ethernet"], optional = true }
 embassy-usb = { version = "0.1.0", path = "../../embassy-usb", features = ["defmt", "msos-descriptor",], optional = true }
 embedded-io = { version = "0.5.0", features = ["defmt-03"]  }
diff --git a/examples/nrf52840/memory.x b/examples/nrf52840/memory.x
index 9b04edec..e4256a5b 100644
--- a/examples/nrf52840/memory.x
+++ b/examples/nrf52840/memory.x
@@ -2,6 +2,6 @@ MEMORY
 {
   /* NOTE 1 K = 1 KiBi = 1024 bytes */
   /* These values correspond to the NRF52840 with Softdevices S140 7.0.1 */
-  FLASH : ORIGIN = 0x00000000, LENGTH = 1024K
-  RAM : ORIGIN = 0x20000000, LENGTH = 256K
+  FLASH : ORIGIN = 0x00000000, LENGTH = 512K
+  RAM : ORIGIN = 0x20000000, LENGTH = 64K
 }
diff --git a/examples/nrf52840/src/bin/blinky.rs b/examples/nrf52840/src/bin/blinky.rs
index 513f6cd8..2b8675f0 100644
--- a/examples/nrf52840/src/bin/blinky.rs
+++ b/examples/nrf52840/src/bin/blinky.rs
@@ -10,7 +10,7 @@ use {defmt_rtt as _, panic_probe as _};
 #[embassy_executor::main]
 async fn main(_spawner: Spawner) {
     let p = embassy_nrf::init(Default::default());
-    let mut led = Output::new(p.P0_13, Level::Low, OutputDrive::Standard);
+    let mut led = Output::new(p.P0_17, Level::Low, OutputDrive::Standard);

     loop {
         led.set_high();
```