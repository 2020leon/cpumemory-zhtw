# 6.5.6. CPU 與節點集合

藉由將程式改變為使用目前為止所描述的介面來為 SMP 與 NUMA 環境調整程式，在來源[^譯註]無法取得的情況下可能會極為昂貴（或者不可能）。此外，系統管理員可能想要對使用者和／或行程能夠使用的資源施加限制。對於這些情境，Linux 系統核心支援了所謂的 CPU 集。這個名稱有一點誤導，因為記憶體節點也被涵蓋其中。它們也與 `cpu_set_t` 資料型別無關。

此刻，CPU 集的介面為一個特殊的檔案系統。它通常沒有被掛載（mount）（至少到目前為止）。這能夠使用

`mount -t cpuset none /dev/cpuset`

改變。掛載點 `/dev/cpuset` 在這個時間點當然必須存在。這個目錄的內容為預設（根）CPU 集的描述。它起初由所有的 CPU 與所有的記憶體節點所構成。這個目錄中的 `cpus` 檔案顯示了在 CPU 集中的 CPU、`mems` 檔案顯示了記憶體節點、`tasks` 檔案顯示了行程。

為了建立一個新的 CPU 集，只要在階層結構中的某個地方建立一個新的目錄。新的 CPU 集會繼承來自父集合的所有設定。接著，新的 CPU 集的 CPU 與記憶體節點能夠藉由將新值寫到在新目錄中的 `cpus` 與 `mems` 虛擬檔案來更改。

若是一個行程屬於一個 CPU 集，CPU 與記憶體節點的設定會被用作親和性與記憶體策略位元遮罩的遮罩。這表示，程式無法在親和性遮罩裡選擇不在行程正在使用的 CPU 集（即，它在 `tasks` 檔案中列出的位置）的 `cpus` 檔案中的任何 CPU。對於記憶體策略的節點遮罩與 `mems` 檔案也是類似的。

除非位元遮罩在遮罩後為空，否則程式不會經歷任何錯誤，因此 CPU 集是一種控制程式執行的近乎無形的手段。這種方法在有著大量 CPU 與／或記憶體節點時是尤其有效率的。將一個行程移到一個新的 CPU 集，就跟將行程 ID 寫到合適 CPU 集的 `tasks` 檔案一樣簡單。

CPU 集的目錄包含許多其它檔案，能用來指定像是記憶體壓力下、以及獨占存取 CPU 與記憶體節點時的行為。感興趣的讀者請參閱系統核心原始碼樹中的檔案 `Documentation/cpusets.txt`。


[^譯註]: 根據前後文猜測，這裡的「來源」指的應該是程式使用的 CPU 與記憶體節點。