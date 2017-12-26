# coding: utf-8
require 'yaml'

PROGRAMS = ["lelev", "lconst", "omegau"]
RAWS = PROGRAMS.map { |p| "build/raw/#{p}.raw" }
puts RAWS

task :default => RAWS

directory "build"

rule ".raw" => [->(f){gen_source_for_raw(f)}, "build"] do |task|
  mkdir_p task.name.pathmap("%d")
  `./txt2raw.pl #{task.source} #{task.name}`
end

rule ".raw" => [->(f){source_for_raw(f)}, "build"] do |task|
  mkdir_p task.name.pathmap("%d")
  `./txt2raw.pl #{task.source} #{task.name}`
end

task "build/hp42s/lelev.hp42s" => "data/eVs.txt" do |task|
  mkdir_p task.name.pathmap("%d")
  lines = File.open(task.source).each_line
  File.open(task.name, "w") do |file|
    file.write("LBL \"LELEV\"\n")
    save_stack_regs(file) do
      [lines.count, 1, "NEWMAT",
       "STO \"ELEV\"",
       "INDEX \"ELEV\""].each do |x|
        file.write("#{x}\n")
      end
      lines.rewind
      lines.each do |l|
        [l.strip, "STOEL", "I+"].each do |x|
          file.write("#{x}\n")
        end
      end
    end
    file.write("END")
  end
end

task "build/hp42s/lconst.hp42s" => "data/constants.yaml" do |task|
  mkdir_p task.name.pathmap("%d")
  constants = File.open(task.source) do |file|
    YAML.load(file.read)
  end
  File.open(task.name, "w") do |file|
    file.write("LBL \"LCONST\"\n")
    save_stack_regs(file) do
      constants.each do |k,v|
        # txt2raw.pl requires uppercase E in scientific notation
        ["#{v}".sub("e", "E"),"STO \"#{k}\""].each do |x|
          file.write("#{x}\n")
        end
      end
    end
    file.write("END")
  end
end

def gen_source_for_raw(raw_file)
  raw_file.pathmap("%{^build/raw/,build/hp42s/}X.hp42s")
end

def source_for_raw(raw_file)
  raw_file.pathmap("%{^build/raw/,src/}X.hp42s")
end

def save_stack_regs(file)
  ["STO \"QES_X\"",
   "Rv",
   "STO \"QES_Y\"",
   "Rv",
   "STO \"QES_Z\"",
   "Rv",
   "STO \"QES_T\"",
   "Rv",
   "LASTX",
   "STO \"QES_L\"",
   "Rv",
   "RCL \"REGS\"",
   "STO \"QREGS\"",
   "Rv",
   25, 1,
   "NEWMAT",
   "STO \"REGS\""].each do |x|
    file.write("#{x}\n")
  end
  yield
  ["RCL \"QREGS\"",
   "CLV \"QREGS\"",
   "STO \"REGS\"",
   "RCL \"QES_L\"",
   "CLV \"QES_L\"",
   "STO ST L",
   "RCL \"QES_T\"",
   "CLV \"QES_T\"",
   "RCL \"QES_Z\"",
   "CLV \"QES_Z\"",
   "RCL \"QES_Y\"",
   "CLV \"QES_Y\"",
   "RCL \"QES_X\"",
   "CLV \"QES_X\""].each do |x|
    file.write("#{x}\n")
  end
end
